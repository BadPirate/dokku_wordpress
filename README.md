# Wordpress 4.7.3 for Dokku
This is a bare bones installation with a few tweaks to allow for easy and rapid installation on dokku.

## But Why?

If you are like me and you want an easy way to deploy multiple wordpress endpoints to the same server (for instance a Digital Ocean droplet), and having the flexibility to add other microservices, then Dokku is the way to go.

# Installation
## Prepare Dokku Server

If necessary, install mysql plugin:

```
sudo dokku plugin:install https://github.com/dokku/dokku-mysql.git mysql
```

Then, from a user with sudo on dokku machine, choose the application name for your Wordpress blog as well as a name for the DB to use, set those values locally, this will be used for all the other settings:

```
WP_APP_NAME=<your app name>
WP_DB_NAME=<your db name>
```

Next, copy and paste this code to create the app, create the DB, link the DB, generate and set all of the hash keys and nonces required for Wordpress and setup persistence for the upload, themes and plugins content directories.

```
dokku apps:create $WP_APP_NAME
dokku mysql:create $WP_DB_NAME && dokku mysql:link $WP_DB_NAME $WP_APP_NAME
dokku config:set $WP_APP_NAME AUTH_KEY=`uuidgen` SECURE_AUTH_KEY=`uuidgen` LOGGED_IN_KEY=`uuidgen` NONCE_KEY=`uuidgen` AUTH_SALT=`uuidgen` SECURE_AUTH_SALT=`uuidgen` LOGGED_IN_SALT=`uuidgen` NONCE_SALT=`uuidgen`
dokku docker-options:add $WP_APP_NAME run "-v /opt/$WP_APP_NAME/wp-content/plugins:/app/wp-content/plugins"
dokku docker-options:add $WP_APP_NAME run "-v /opt/$WP_APP_NAME/wp-content/themes:/app/wp-content/themes"
dokku docker-options:add $WP_APP_NAME run "-v /opt/$WP_APP_NAME/wp-content/uploads:/app/wp-content/uploads"
dokku docker-options:add $WP_APP_NAME deploy "-v /opt/$WP_APP_NAME/wp-content/plugins:/app/wp-content/plugins"
dokku docker-options:add $WP_APP_NAME deploy "-v /opt/$WP_APP_NAME/wp-content/themes:/app/wp-content/themes"
dokku docker-options:add $WP_APP_NAME deploy "-v /opt/$WP_APP_NAME/wp-content/uploads:/app/wp-content/uploads"
```

## Deploy app to Dokku

1. Clone this repo
2. Add dokku remote: `git remote add dokku dokku@<my dokku server>:<my dokku app>`
3. Push to remote: `git push dokku master`