# Datahub docker-compose file  

This configuration file allows  
- Easy reconfiguration of the authentication method via `jaas.conf` and `application.conf`
- Enabled LDAP authentication with environment variable

## Steps to deploy the sandbox server for datahub with ldap integration  
1. Create a directory `customization` at the location with `docker-compose.yml`  
2. Move file `jaas.example.conf` and `application.example.conf` into the directory and change the name to `jass.conf` and `application.conf` accordingly.  
3. Reconfigure the file as needed  
4. Create an environment file `.env` at the same level as `docker-compose.yml` and configure the parameters to suit the workflow
5. Run the compose command `docker compose up`. (If compose file name was changed then use `docker compose -f <filename> up` to specify the compose file)
6. Any change to the configuration file in folder `customization` must be accompanied by restarting the datahub compose to apply the change

> Note that the file `application.conf` controls all the authentication procedures and if no authentication is set to `true`, all the `auth.<method>.enabled` is `false`. Users won't be able to login.

## Simple Demo organization  
All configuration for domain `example.com` is in the folder `example`.  
This include custom configurations for application frontend and JaaS module to authenticate user with `uid` as username.
Another file, `demo-org.ldif`, is used to create example organization via simple command  

```ldapadd -x -D "cn=admin,dc=example,dc=com" -W -f demo-org.ldif```  

Here `-D cn=admin,dc=example,dc=com` we use admin user to do searching for user entry where `-W` flag enable password input (more secure).

The last file, `ldap-recipe.yml` is the connection definition for datahub to ingest users from ldap server to create datahub users.

## Demo organization ldif for ldap server for AD schema
The `.ldif` files must be ingest in specific order to work. 
1. begin with `AD-schema.ldif` to create AD schema 
2. then `sample-org.ldif` 
3. moving to create a set of user `sample-user.ldif` 
4. create a groupOfNames using `sample-group.ldif`
5. (Optional) `load-overlay.ldif` and `enable-memberof.ldif` require root privilege to configure
