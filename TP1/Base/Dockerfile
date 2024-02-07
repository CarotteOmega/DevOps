# our base image
FROM postgres:14.1-alpine


   

# copy files required for the /docker-entrypoint-initdb.d to run
COPY ./Script/CreateScheme.sql /docker-entrypoint-initdb.d/
COPY ./Script/InsertData.sql /docker-entrypoint-initdb.d/
