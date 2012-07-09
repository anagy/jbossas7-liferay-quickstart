Liferay on OpenShift
===================

This git repository helps you get up and running quickly with Liferay Portal installation
on OpenShift.  

By default the backend database is MySQL and the database name is the
same as your application name (using $_ENV['OPENSHIFT_APP_NAME']).  You can name
your application and database name to whatever you want.  

However, per default the name of the database will always match the application so you might have to update *.openshift/action_hooks/deploy*.


Running on OpenShift
--------------------

If you don't have an Openshift account you can create an account at http://openshift.redhat.com/ 

Create a JBoss AS 7.1 or JBoss EAP 6.0 server
---------------------------------------------
         rhc app create -a $Your-App-Name -t jbossas-7    -g medium
	                    (or)
         rhc app create -a $Your-App-Name -t jbosseap-6.0 -g medium
    
* Note: For deploying Liferay, the application max heap size should be set to 1024m which is available only from medium and above gears, for more info check https://openshift.redhat.com/community/developers/pricing
	
Add DB support to your application
----------------------------------
          rhc app cartridge add -a $Your-App-Name -c mysql-5.1
          rhc app cartridge add -a $Your-App-Name -c phpmyadmin-3.4 (optional)
		
Application Info
----------------
        rhc app show -a $Your-App-Name


Add this upstream jbossas7-liferay-quickstart repo
--------------------------------------------------

        cd $Your-App-Name
        git remote add upstream -m master git://github.com/openshift/jbossas7-liferay-quickstart.git
        git pull -s recursive -X theirs upstream master
	
   Then push the repo to your openshift cloud repo dir

        git push

   That's it, you can now checkout your application at:

         http://$Your-App-Name-$yournamespace.rhcloud.com
         
__Note__ : 
_Sometimes it takes longer time for the server to start and deploy, please watch the status of server on $OPENSHIFT_LOG_DIR/server.log.  It could be due the issue mentioned in **Open Issues**_

Default Credentials
-------------------

**Default Admin Username:** test@liferay.com

**Default Admin Password:** test

Repo layout
-----------

**customization/** 

All Liferay Portal customizations

     portal-ext.properties           - the standard customizations that will be done for Liferay installations
    jboss-deployment-structure.xml   - the updated jboss-deployment-structure for JBoss As 7.1
		
**.openshift/action_hooks/deploy** 

Script that gets run every push deploying your application

**.openshift/action_hooks/deploying-war-info**

The war file information of liferay that will be deployed

Liferay Portal Database configuration
-------------------------------------

The JBoss AS 7.1 or JBoss EAP 6.0 cartridge will create two datasources by default one for MySQL and another for Postgesql.  The following are 
the JNDI names for the dataources
		MySQL      - java:jboss/datasources/MysqlDS
		PostgreSQL - java:jboss/datasources/PostgreSQLDS

By default this quick start will configure MySQL database as default database for the Portal

This is configured in the $REPO_DIR/customization/ROOT.war/WEB-INF/classes/portal-ext.properties entry,
	
	jdbc.default.jndi.name=java:jboss/datasources/MysqlDS
		
If you want to change it to Postgresql then you need to update the $REPO_DIR/customization/ROOT.war/WEB-INF/classes/portal-ext.properties

	jdbc.default.jndi.name=java:jboss/datasources/PostgreSQLDS
	
Upgrade the Portal
------------------

In order to update or upgrade to the latest liferay portal, you'll need to re-pull
and re-push.

        Pull from upstream
        ------------------

         cd $Your-App-Name/
         git pull -s recursive -X theirs upstream master

        Push the new changes upstream
        -----------------------------

        Update the file and modify the first line with correct war file name that will be used for upgrade default is liferay-portal-6.1.0-ce-ga1-20120106155615760.war, but during update you will change this to something like liferay-portal-6.1.1-ce-ga1-xxxxxxxxx.war
 
        .openshift/action_hooks/deploying-war-info - the war file of liferay that will be deployed

         git push

       
The deploy script will then automatically pull the Liferay Portal war file from the sourceforge site and explode the same over the ROOT.war


Upcoming features
----------------

* Standard maven based directory structure for the application and maven deployment
* Postgresql support for Liferay, import/export sql scripts

Open Issue
----------

* Right now the JBoss Application server is trying to deploy the Liferay Portal ROOT.war twice, which is making the server delay in deploying portal 
