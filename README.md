SiLCC
=====

Synopsis
--------

SiLCC aims to provide a learning NLP based relevancy filter/tagger/classifier that is 
applied to the various information sources that Swift aggregates (RSS, twitter, SMS). 

This software is released under the [GNU Lesser General Public License](http://www.gnu.org/copyleft/lesser.html).

Change Log
--------

* v0.1.0: Initial stable release of the software.

Installation
------------

To set up a silcc server instance:

1) Create a Python virtualenv

    $ virtualenv ~/SiLCC-env

2) In the this distribution run:

    $ python setup.py develop

This should install all needed libraries in your virtual environment.

Make sure you have ``MySQLdb`` installed:

    $ easy_install mysql-python

You will need the Python development libraries as well as the MySQL client development libraries installed for this to work. On Ubuntu you need the ``libmysqlclient-dev`` and ``python-dev`` packages:

    $ sudo aptitude install libmysqlclient-dev python-dev

Make sure you have the correct version of ``SQLAlchemy`` installed:

    $ easy_install -U SQLAlchemy==0.6.0

3) You also need to install nltk. Download it from the following location:

    $ wget http://nltk.googlecode.com/files/nltk-2.0b8.zip
    $ unzip nltk-2.0b8.zip
    $ cd nltk-2.0b8

Now make sure you have activated the virtualenv created in step 1.

    $ source ~/SiLCC-env/bin/activate

If you are sure its activated, install PyYAML.

    $ easy_install pyyaml

Issue the following to install nltk into your virtualenv.

    $ python setup.py install

4) After installing nltk you need to download one
of the Treebank Corpus for the part-of-speech tagger to work:

    $ python -c "import nltk;nltk.download('maxent_treebank_pos_tagger')"

5) Install numpy. On Debian-based distributions the easiest is to install the package:

    $ aptitude install python-numpy

6) Create the database. If using MySQL:

    $ mysql -u root -p
    mysql> create database silcc default charset utf8;
    mysql> grant all on silcc.* to silcc@localhost identified by 'password';

Make sure the .ini file contains a DB URI to the above database.

(Please see instructions below on using SQLAlchemy migrate scripts to generate the db schema)

7) To start the server:

    $ paster serve --daemon development.ini

Look for any errors in paster.log

You can also run it interactively by excluding ``--daemon``:

    $ paster serve development.ini

Placing your SiLCC Instance DB under version control
----------------------------------------------------

You may want to place your database under version control so 
that you can easily upgrade the schema, should it evolve over time.

To do this you need to first create an
empty database, and then place it under version control.

1) First install ``sqlalchemy-migrate``:

    $ easy_install -U sqlalchemy-migrate==0.5.4

2) now place your database under version control with:

    $ python db_repository/manage.py version_control mysql://silcc:password@localhost:3306/silcc

This will create the migrate_version table in your database and set the
initial version to 0.

After that you may run any schema upgrade scripts including the
first one which will create the necessary tables.

Most data in the SiLLC distribution can be recreated using scripts and
data dumps provided.

3) To make upgrading your local instance easier its best to create a manage.py
scripts as follows:

    $ migrate manage manage.py --repository=db_repository --url=mysql://silcc:password@localhost:3306/silcc

Since the manage.py will be specific to your local instance it is not included in the SiLCC repo.

4) Now run a db update to generate the initial schema:

    $ python manage.py upgrade

5) The Web Service API needs to have one or more valid keys in the apikey table.
For testing purposes add a key with a valid_domains value of ``*`` (valid from all domains)

    $ mysql -u root -p
    mysql> insert into apikey set keystr = 'AAAABBBB', valid_domains = '*';

Deploying on Rackspace
----------------------

1. Create an instance of Ubuntu 10.04 LTS (Lucid Lynx).
2. Open up a SSH terminal to your new instance.
3. Run one of the two deployment scripts, depending on the type of installation. Each time you are prompted for a password, this will be the MySQL root password of your choosing. It should be safe to leave this blank.
    * Development: ``curl https://github.com/ushahidi/SiLCC/raw/master/deploy/ubuntu-lucid-development.sh | bash``
    * Production: ``curl https://github.com/ushahidi/SiLCC/raw/master/deploy/ubuntu-lucid-production.sh | bash``
4. Open your browser and point it to: ``http://ip.or.hostname.of.your.new.instance:5002/``

Bindings
--------

For your convenience, the following language-specific API "proxy" libraries are available:

* [SiLCC-PHP-Binding](https://github.com/ushahidi/SiLCC-PHP-Binding)
