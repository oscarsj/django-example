
This git repository helps you get up and running quickly w/ a Django installation
on OpenShift Express.  The Django project name used in this repo is 'openshift'
but you can feel free to change it. Now the backend is MySQL.

This git repository helps you get up and running quickly w/ a Django
installation on OpenShift.  The Django project name used in this repo
is 'openshift' but you can feel free to change it.  Right now the
backend is sqlite3 and the database runtime is @
$OPENSHIFT_DATA_DIR/sqlite3.db.

Before you push this app for the first time, you will need to change
the Django admin password (see below). Then, when you first push this
application to the cloud instance, the sqlite database is copied from
wsgi/openshift/sqlite3.db with your newly changed login
credentials. Other than the password change, this is the stock
database that is created when 'python manage.py syncdb' is run with
only the admin app installed.

On subsequent pushes, a 'python manage.py syncdb' is executed to make
sure that any models you added are created in the DB.  If you do
anything that requires an alter table, you could add the alter
statements in GIT_ROOT/.openshift/action_hooks/alter.sql and then use
GIT_ROOT/.openshift/action_hooks/deploy to execute that script (make
sure to back up your database w/ 'rhc app snapshot save' first :) )


Running on OpenShift
--------------------

Create an account at http://openshift.redhat.com/

Install the RHC client tools if you have not already done so:
    
    sudo gem install rhc

Create a python-2.6 application

    rhc app create -a django -t python-2.6

Add MySQL Database service

    rhc-ctl-app -a django -e add-mysql-5.1

Add PHPMyAdmin service

    rhc-ctl-app -a django -e add-phpmyadmin-3.4

Add this upstream seambooking repo

    cd django
    git remote add upstream -m master git@github.com:drivard/openshift-django-mysql.git
    git pull -s recursive -X theirs upstream master

Then push the repo upstream

    git push
	
**Note**: As the git push output scrolls by, keep an eye out for a
  line of output that starts with 'CLIENT_MESSAGE: '. This line
  contains the generated admin password that you will need to begin
  administering your Django app. This is the only time the password
  will be displayed, so be sure to save it somewhere!

Create the Django admin user

Find your openshift app UUID

    rhc domain

Connect through ssh to your app

    ssh $uuid@django-$yournamespace.rhcloud.com

Get in the app directory

    cd django/repo/wsgi/openshift

Export the python egg cache directory

    export PYTHON_EGG_CACHE=$OPENSHIFT_GEAR_DIR/virtenv/lib/python-2.6

Create the admin user

    ../../../../virtenv/bin/python ./manage.py createsuperuser

That's it, you can now checkout your application at (Using the user you just created.):

    http://django-$yournamespace.rhcloud.com
