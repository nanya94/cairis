---
layout: default 
title: Installing CAIRIS
---

Thanks go to [Robin Quetin](https://github.com/RobinQuetin) for putting these instructions together.

1. Install the required applications and dependencies:

        $ sudo apt-get install python-wxglade python-glade2 python-wxgtk2.8 python-dev build-essential mysql-server mysql-client graphviz docbook dblatex python- pip git libmysqlclient-dev --no-install-recommends texlive-latex-extra
2. Install the Python extensions:

        $ sudo pip install mysql-python==1.2.3 pyparsing==1.5.7 pydot
3. Clone the CAIRIS repository into a directory, for example /opt/:

        $ CAIRIS_DIR=/opt/cairis
        $ sudo git clone https://github.com/failys/CAIRIS.git $CAIRIS_DIR
4. Create a new user group, and change permissions

        $ CAIRIS_DIR=/opt/cairis
        $ CAIRIS_USER=cairis
        $ sudo useradd --no-create-home --system $CAIRIS_USER 
        $ sudo usermod -a -G $CAIRIS_USER $USER
        $ sudo chown -R $CAIRIS_USER:$CAIRIS_USER $CAIRIS_DIR $ sudo chmod -R 775 $CAIRIS_DIR
5. Create a new database for CAIRIS with a MySQL user account which has full access to the database, for example:

        > GRANT USAGE ON *.* TO 'cairis'@'localhost' IDENTIFIED BY 'cairis123' WITH MAX_QUERIES_PER_HOUR 0 MAX_CONNECTIONS_PER_HOUR 0 MAX_UPDATES_PER_HOUR 0 MAX_USER_CONNECTIONS 0;
        > CREATE DATABASE IF NOT EXISTS ``cairis``;
        > GRANT ALL PRIVILEGES ON ``cairis``.* TO 'cairis'@'localhost';
6. Create the database tables and stored procedures required by CAIRIS

        $ CAIRIS_SQL=$CAIRIS_DIR/cairis/cairis/sql
        $ mysql –u cairis –p –database=cairis < $CAIRIS_SQL/procs.sql
        $ mysql –u cairis –p –database=cairis < $CAIRIS_SQL/init.sql
7. To view certain models in CAIRIS, change the maximum recursion depth for stored procedures to 255 from the default of 0.

        $ mysql -h localhost -u root -p << !
        set global max_sp_recursion_depth = 255; flush tables;
        flush privileges;
        !
8.  Create a configuration file for CAIRIS.  By default, CAIRIS looks for a configuration file in the home directory of the user.  You can use the template configuration file provided with CAIRIS as your base configuration.

        $ mkdir -p ~/CAIRIS/cairis/cairis/config/
        $ cp $CAIRIS_DIR/cairis/cairis/config/cairis.cnf ~/CAIRIS/cairis/cairis/config/
        $ sudo chown -R $USER:$USER ~/CAIRIS
9.  Change the values of the configuration file according to your installation.  Note that the 'root' is the directory of the CAIRIS application, which in our our case is `$CAIRIS_DIR/cairis`.

        dbhost = 127.0.0.1
        dbport = 3306
        dbuser = cairis
        dbpasswd = cairis123
        dbname = cairis
        tmp_dir = /tmp
        root = /opt/cairis/cairis
10.  To start CAIRIS, you can open a terminal window and go to the directory containing the source code of CAIRIS.  This is usually `$CAIRIS_DIR/cairis`.

        $ python cairis.py