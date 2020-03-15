# Docker MSSQL Server 

## Pull image:
docker pull mcr.microsoft.com/mssql/server

## Run container:
- tips: https://hub.docker.com/_/microsoft-mssql-server
- https://docs.microsoft.com/en-us/sql/linux/quickstart-install-connect-docker?view=sql-server-ver15&pivots=cs1-bash
- stackoverflow: https://stackoverflow.com/questions/45712122/connection-string-for-sqlserver-in-docker-container
- A strong system administrator (SA) password: At least 8 characters including uppercase, lowercase letters, base-10 digits and/or non-alphanumeric symbols.
```bash
docker run \
-e 'ACCEPT_EULA=Y' \
-e 'SA_PASSWORD=Password1!' \
-e 'MSSQL_PID=Express' \
--name sqlserver \
-p 1433:1433 -d mcr.microsoft.com/mssql/server:latest

# Enter the container:
docker exec -it sqlserver "bash"
# Launch sqlcmd from container command line:
/opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "Password1!"
# Interact with the database from the command line
CREATE DATABASE heroes
GO
USE heroes
CREATE TABLE HeroValue (id INT, name VARCHAR(50))
INSERT into HeroValue VALUES (1, "Wonder Woman")
GO
SELECT * FROM HeroValue;
GO
```

## Dockerfile
- big thanks to: https://github.com/twright-msft/mssql-node-docker-demo-app
- https://www.sqlshack.com/creating-your-own-sql-server-docker-image/
- https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-configure-docker?view=sql-server-ver15
- https://stackoverflow.com/questions/45712122/connection-string-for-sqlserver-in-docker-container
- We need to write a Dockerfile that automates database construction so that we can run the image and it will create a database, a table, and insert some preliminary data.
- The container will use scripts that will be copied into the container and executed on container startup.
    1. Dockerfile - selects the image and specifies startup commands
    2. entrypoint.sh - called from Dockerfile, launches sql server and creates db
    3. import-data.sh - called from Dockerfile entrypoint
    4. setup.sql - called from import-data.sh, constructs the database
    
Build the docker image
```bash
docker build -t mssql:dev . 
```
Run the image and create a container
```
docker run \
-e 'ACCEPT_EULA=Y' \
-e 'SA_PASSWORD=Password1!' \
-e 'MSSQL_PID=Express' \
--name sqlserver \
-p 1433:1433 \
-d mssql:dev
```
Access the container to confirm it is working.
```bash
docker exec -it sqlserver "bash"
# from container command line
/opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "Password1!" -d heroes
# Run from sqlcmd command line
# For some reason, it only shows the last entry, but they are all there.
SELECT * FROM HeroValue;
GO
```

## .NET Core connection string
```c#
"ConnectionStrings": {
    "DockerDB": "Server=localhost,1433;Database=heroes;User ID=SA;Password=Password1!"
  }
```


