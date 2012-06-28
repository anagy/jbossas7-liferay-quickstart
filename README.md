										!!!! WARNING WORK IN PROGRESS!!!!

Liferay on OpenShift
===================

This git repository helps you get up and running quickly with Liferay Portal installation
on OpenShift.  The backend database is MySQL and the database name is the
same as your application name (using $_ENV['OPENSHIFT_APP_NAME']).  You can name
your application whatever you want.  However, per default the name of the database
will always match the application so you might have to update *.openshift/action_hooks/deploy*.


Running on OpenShift
--------------------

Create an account at http://openshift.redhat.com/

Create a JBoss AS 7.1 application

    rhc app create -a lportal61 -t jbossas-7 -g medium

Add MySQL support to your application

    rhc app cartridge add -a lportal61 -c mysql-5.1

Add this upstream drupal repo

    cd lportal61
    git remote add upstream -m master git://github.com/kameshsampath/liferay-portal-quickstart
    git pull -s recursive -X theirs upstream master

Then push the repo upstream

    git push

That's it, you can now checkout your application at:
    http://lportal61-$yournamespace.rhcloud.com

Default Credentials
-------------------
<table>
<tr><td>Default Admin Username</td><td>test@liferay.com</td></tr>
<tr><td>Default Admin Password</td><td>test</td></tr>
</table>

Updates
-------

In order to update or upgrade to the latest liferay portal, you'll need to re-pull
and re-push.

Pull from upstream:

    cd lportal61/
    git pull -s recursive -X theirs upstream master

Push the new changes upstream

Update the file and modify the first line with correct war file name that will be used for upgrade default is liferay-portal-6.1.0-ce-ga1-20120106155615760.war, but during update you will change
this to something like liferay-portal-6.1.1-ce-ga1-xxxxxxxxx.war
    .openshift/action_hooks/deploying-war-info - the war file of liferay that will be deployed

     git push

The deploy script will then automatically pull the Liferay Portal war file from the sourceforge site
and explode the same over the ROOT.war


Repo layout
-----------

*customization/* - Externally exposed php code goes here
*.openshift/action_hooks/deploy* - Script that gets run every push deploying your application
*.openshift/action_hooks/deploying-war-info* - the war file of liferay that will be deployed


Upcoming features
----------------

* Standard maven based directory structure for the application and maven deployment