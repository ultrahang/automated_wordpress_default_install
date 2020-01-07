# automated_wordpress_default_install
This project installs a default multisite wordpress with json api enabled and also adds a plugin as reference for plugin installations.

There are two docker containers created for wordpress using the official images:
- awdi : the default wordpress container using the official wordpress image
- awdi_db : the default mysql container using the official mysql image

There is an additional wordpress configuration toolbox container which has
- wp-cli installed for configuring the wordpress site
- dockerize installed for waiting for the database startup as a dependency for the configuration tasks

The docker-compose.yml in the root directory contains the options for the wordpress and database installation like:

environment options for the awdi_db:

      MYSQL_ROOT_PASSWORD: password
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: password

environment options for the wordpress configuration toolbox:

      WORDPRESS_LOCALE: fr_FR
      WORDPRESS_DB_HOST: awdi_db
      WORDPRESS_DB_NAME: wordpress
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: password
      WORDPRESS_WEBSITE_TITLE: "My blog"
      WORDPRESS_WEBSITE_URL: "http://localhost"
      WORDPRESS_WEBSITE_URL_WITHOUT_HTTP: "localhost"
      WORDPRESS_WEBSITE_POST_URL_STRUCTURE: "/%year%/%monthnum%/%day%/%postname%/"
      WORDPRESS_ADMIN_USER: "admin"
      WORDPRESS_ADMIN_PASSWORD: "admin"
      WORDPRESS_ADMIN_EMAIL: "test@example.com"

These values can be changed accordingly to the needs of the usage.

The docker/wordpress/toolbox/Dockerfile contains the instructions for creating the interim configuration container.
It installs the following packages:
- WP-CLI
- MySQL extension for DB manipulation
- dockerize for startup dependencies

The docker/wordpress/toolbox directory also contains a Makefile which has a configuration section. 
It can be modified in case more options needs to be changed by the wp-cli or there are multiple other plugins to be installed for the default installation like:

        $(WP_CLI) option update siteurl "${WORDPRESS_WEBSITE_URL}"
        $(WP_CLI) rewrite structure $(WORDPRESS_WEBSITE_POST_URL_STRUCTURE)
        $(WP_CLI) plugin install gutenberg --activate
        
# Usage:

Clone the git repositpory and type:

- make wordpress_install  

This command will install the wordpress and mysql containers and also creates the intermin configuration container and executes the configuration tasks.
As the result there will be three containers:
- awdi
- awdi_db
- awdi_toolbox

In case the wordpress containers (awdi and awdi_db) need to be reinitialized then type:

- make wordpress_reinstall

This command will erase the database and the wordpress files and re-create the awdi and awdi_db containers and reconfigure the wordpress installation using the existing awdi_toolbox container.

To clean up all the three containers type:

- make clean

If there is any modification made on the docker-compose.yml or on the Makefile for changing options and/or adding/removing parameters always re-create the toolbox itself by running:

- make clean
- make wordpress_install

# Troubleshooting

In case any error occurs repeate the command again. There are cases when the synchronization between the file and database operations are not in a perfect shape between the docker containers and re-running the command usually solves the issue without any modification on anything.







