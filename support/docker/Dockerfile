# Use jbossdemocentral/developer as the base
FROM jbossdemocentral/developer

# Maintainer details
MAINTAINER Red Hat

# Environment Variables
ENV HOME /opt/jboss
ENV JBOSS_HOME $HOME/jboss-eap-7.1
ENV BA_VERSION_MAJOR 7
ENV BA_VERSION_MINOR 0
ENV BA_VERSION_MICRO 0
ENV BA_VERSION_PATCH ER4

ENV EAP_VERSION_MAJOR 7
ENV EAP_VERSION_MINOR 1
ENV EAP_VERSION_MICRO 0
#ENV EAP_VERSION_PATCH 0

ENV EAP_ARCHIVE=jboss-eap-$EAP_VERSION_MAJOR.$EAP_VERSION_MINOR.$EAP_VERSION_MICRO.zip
ENV PAM_BUSINESS_CENTRAL=rhpam-$BA_VERSION_MAJOR.$BA_VERSION_MINOR.$BA_VERSION_MICRO.$BA_VERSION_PATCH-business-central-eap7-deployable.zip
ENV PAM_KIE_SERVER=rhpam-$BA_VERSION_MAJOR.$BA_VERSION_MINOR.$BA_VERSION_MICRO.$BA_VERSION_PATCH-kie-server-ee7.zip
ENV PAM_ADDONS=rhpam-7.0.0.ER4-add-ons.zip
ENV PAM_CASE_MGMT=rhpam-7.0-case-mgmt-showcase-eap7-deployable.zip

# ADD Installation Files
COPY installs/$PAM_BUSINESS_CENTRAL installs/$PAM_KIE_SERVER installs/$PAM_ADDONS installs/$EAP_ARCHIVE /opt/jboss/

# Update Permissions on Installers
USER root
RUN chown jboss:jboss /opt/jboss/$EAP_ARCHIVE /opt/jboss/$PAM_BUSINESS_CENTRAL /opt/jboss/$PAM_KIE_SERVER /opt/jboss/$PAM_ADDONS
USER jboss

# Prepare and run installer and cleanup installation components
RUN unzip -qo /opt/jboss/$EAP_ARCHIVE -d $HOME && \
    unzip -qo /opt/jboss/$PAM_BUSINESS_CENTRAL -d $HOME  && \
		unzip -qo /opt/jboss/$PAM_KIE_SERVER -d $JBOSS_HOME/standalone/deployments && touch $JBOSS_HOME/standalone/deployments/kie-server.war.dodeploy && \
    unzip -qo /opt/jboss/$PAM_ADDONS $PAM_CASE_MGMT -d $HOME && unzip -qo $HOME/$PAM_CASE_MGMT -d $HOME && touch $JBOSS_HOME/standalone/deployments/rhpam-case-mgmt-showcase.war.dodeploy && \
    rm -rf /opt/jboss/$PAM_BUSINESS_CENTRAL /opt/jboss/$PAM_KIE_SERVER /opt/jboss/$PAM_ADDONS $HOME/$PAM_CASE_MGMT /opt/jboss/$EAP_ARCHIVE $JBOSS_HOME/standalone/configuration/standalone_xml_history/

# Add support files
RUN $JBOSS_HOME/bin/add-user.sh -a -r ApplicationRealm -u pamAdmin -p redhatpam1! -ro analyst,admin,manager,user,kie-server,kiemgmt,rest-all --silent  && \
  $JBOSS_HOME/bin/add-user.sh -a -r ApplicationRealm -u kieserver -p kieserver1! -ro kie-server --silent
COPY support/standalone-full.xml $JBOSS_HOME/standalone/configuration/
COPY support/userinfo.properties $JBOSS_HOME/standalone/deployments/business-central.war/WEB-INF/classes/

# Swtich back to root user to perform cleanup
USER root

# Fix permissions on support files
RUN chown -R jboss:jboss $JBOSS_HOME/standalone/configuration/standalone-full.xml $JBOSS_HOME/standalone/deployments/business-central.war/WEB-INF/classes/userinfo.properties

# Run as JBoss
USER jboss

# Expose Ports
EXPOSE 9990 9999 8080

# Run BRMS
ENTRYPOINT ["/opt/jboss/jboss-eap-7.1/bin/standalone.sh"]
CMD ["-c","standalone-full.xml","-b", "0.0.0.0","-bmanagement","0.0.0.0"]
