# Creating a Data Lake to organise data pipeline for exploration of Twitter sentiment impact on cryptocurrency valuation
Cryptocurrency is a relatively new asset class and is still subject to a lot of uncertainty and volatility. As a result, cryptocurrency prices can be highly unpredictable and subject to sudden fluctuations. 

Investor sentiments play a particular role in moving the valuation of crypto. For example, if investors have a positive outlook towards the future of a particular cryptocurrency, they may be more willing to invest in it, leading to an increase in price. Similarly, if investors have a negative outlook towards a cryptocurrency, they may sell their holdings, leading to a decrease in price. Impact of investor/ influencer sentiment on cryptocurrency is our main area of research in this project. 

High-level overview of the lakehouse architecture:

![Screenshot 2023-06-07 at 6 08 16 PM](https://github.com/saadiahumayun/data-lake/assets/34272512/b7493ea4-c3e2-40f8-9c35-adcf3f1514cc)

**DATA SOURCES:**

We have 2 data sources to lay down our big data pipeline.


1. Real-time tweets of cryptocurrency investors and infuencers
2. Yahoo Finance data on cryptocurrency price movement.

**Technology Stack**


- Minio
- Jupyter Notebook
- Spark (for tweets NLP, ML and data processing)
- Postgres SQL
- Apache Superset

Here's a high-level overview of how the above technologies can be connected to build a data lake:


1. Data ingestion with Python+Minio: Minio is used as an S3 storage to ingest data from various sources coupled with Python. All raw data will be stored in Minio.

2. Data processing with Spark: Apache Spark to be used as a big data processing engine to process and analyze the ingested data. Spark can handle both batch and real-time processing and provide scalable and distributed computing. Processed data will be stored in Postgres SQL.

3. Data preprocessing on Jupyter-Pyspark: Spark to integrate data from Kafka, Cassandra, and Postgres. Spark can provide a unified view of the data, regardless of its source or format.

4. Data storage with Postgres: PostgreSQL will be used as a relational database to store structured data. PostgreSQL provides a rich set of features for data management, including ACID compliance and support for complex queries.

6. Data visualization and reporting: Apache Superset to be used as a visualization and reporting tool to interact with and derive insights from the processed data.

All of the above will be built on docker containers and will interact over docker network.

Overall, we believe this architecture provides a scalable, reliable, and efficient way to build a data lake that can handle real-time data ingestion, storage, and processing. It also allows us to integrate and analyze data from various sources using Spark, and visualize the results using reporting and visualization tools.

# Getting Started with Setting up the Containers #

## Docker Images 
- For ease of use, before starting services, please pull the required docker images first.
- Run the following commands in order to pull the required docker images.

~~~
docker pull apache/airflow:2.6.1
~~~
~~~
docker pull bde2020/spark-master:3.2.0-hadoop3.2
~~~
~~~
docker pull bde2020/spark-worker:3.2.0-hadoop3.2
~~~
~~~
docker pull apache/superset
~~~
~~~
docker pull jupyter/pyspark-notebook:latest
~~~
~~~
docker pull postgres:13
~~~
~~~
docker pull bitnami/zookeeper:3.7.0
~~~
~~~
docker pull minio/minio
~~~
~~~
docker pull minio/mc
~~~
~~~
docker pull redis:latest
~~~

## Dockerfile: Build the Image.
- A `Dockerfile`  is a text document that contains all the commands a user could call on the command line to assemble an image. 
- `Dockerfile` that contians installations of `JAVA-JDK.v11`, `ApacheSpark.v3.3.0`, `Hadoop.v3`, & other dependencies built on top of `Airflow.v.2.2.3`.
- navigate to the `docker-airflow` directory, this is where the `Dockerfile` is located:
    - `Dockerfile` is a `.dockerfile` file that contains the instructions to build the image.
    - `docker` --> `Dockerfile.Spark`.
- It will take about ***10minutes*** to build, depending on yor internet speed / platform you use to build the image.
The Dockerfiles are for building on top of airflow and spark images if you're planning to use Airflow in the pipeline as well. Since, we are not concerned with Airflow as of now, we will leave this for later. Airflow setup will be released in future updates after I have successfully integrated it in my workflow. For now, we will use the other main tech stack.

Navigate to the project directory, then to docker folder. On the CLI, run the command:

~~~
docker compose -f docker-compose.yml -f docker-compose.spark.yml up -d
~~~

To ensure the services are running, you can click on the following URLs:

### Jupyter: http://localhost:8888

* For Jupyter notebook, you must copy the URL with the token generated when the container is started and paste in your browser. 
* The URL with the token can be taken from container logs using:
 
```
docker logs $(docker ps -q --filter "ancestor=jupyter/pyspark-notebook:spark-3.2.0") 2>&1 | grep 'http://127.0.0.1' | tail -1
```
Alternatively, bash into the container
~~~
docker exec -it jupyter_container /bin/bash
~~~
then type
~~~
jupyter server list
~~~

![Screenshot 2023-06-07 at 8 21 56 PM](https://github.com/saadiahumayun/data-lake/assets/34272512/d1020bc6-161e-4a70-9e7f-a67c2d35255a)

Copy-paste the token on the Jupyter console to launch your notebook. You can even set a password with the token to avoid re-generating the token on every login.


### Spark: http://localhost:8181

* Spark Master & Workers.


### Minio: http://localhost:9001

* Minio is the best server which is suited for storing unstructured data such as photos, videos, log files, backups, and container.
* This would serve as our Object Storage Service. 
* Default username and password for minio is `minio_admin` and `minio_password`. 

### Postgres
- Access to the Postgres database is available using the following command:

```
docker exec -it postgres_container psql -U airflow
```
### PgAdmin: [http://localhost:9001](http://localhost:5050/)
* PgAdmin is a web GUI for Postgres.
* Set any password when you are launching it for the first time.

### Configuring Superset: How to use this image
Start a superset instance on port `8080`:
~~~
docker run -d -p 8080:8088 -e "SUPERSET_SECRET_KEY=your_secret_key_here" --name superset apache/superset
~~~
With your local superset container already running...
1. Set up your admin account: 
~~~
docker exec -it superset superset fab create-admin \
              --username admin \
              --firstname Superset \
              --lastname Admin \
              --email admin@superset.com \
              --password admin
~~~
2. Migrate local DB to latest
~~~
docker exec -it superset superset db upgrade
~~~
3. Setup roles
~~~
docker exec -it superset superset init
~~~
4. Login and take a look:  -- navigate to http://localhost:8080/login/ -- u/p: [admin/admin]

# Configuring the services

## Setting up Minio
* Enter Minio username and password to launch your homescreen

![Screenshot 2023-06-07 at 9 26 03 PM](https://github.com/saadiahumayun/data-lake/assets/34272512/be24591e-faac-45d9-8f3a-fb731ede85ac)

* Create your first bucket and dump an supporting data related to your use-case.

![Screenshot 2023-06-07 at 10 03 56 PM](https://github.com/saadiahumayun/data-lake/assets/34272512/25e492cc-ba09-4573-b920-303fb7369fd5)

* Generate your access key and secret key to use with Minio API. This will come handy later when you'll be using python to extract data from Minio. Go to `Access Keys` in the left sidebar and click on `Create access key`. Save the generated tokens on your machine.

![Screenshot 2023-06-07 at 10 05 37 PM](https://github.com/saadiahumayun/data-lake/assets/34272512/de37fbac-a7b3-46b2-83c1-59391419eef9)

Now let's move on to PgAdmin and connect it to our Postgres server.

## Setting up PgAdmin with Postgres
Click on “Add New Server” in the middle of the page under “Quick Links” or right-click on “Server” in the top left and choose “Create” -> “Server…”.
We need to configure the connection detail to add a new database server:
1. In the General tab: choose a name for your database server, e.g. `postgres_db`
2. In the Connection tab: The “Hostname/address” is not “localhost” but the IP address of the postgres container — as the containers will need to talk to each other via the docker network. Since we are on a user defined network, we can just use the hostname `host.docker.internal` instead of a fixed IP-address and let docker take care of DNS resolving in the background.
3. In the Connection tab: The port is the standard application port of the postgres database — `5432`.
4. In the Connection tab: The username and password are specified in the docker-compose.yml as environment variables of the postgres service (airflow and airflow if you haven't yet changed it).

![Screenshot 2023-06-07 at 10 13 16 PM](https://github.com/saadiahumayun/data-lake/assets/34272512/daebd835-ecdb-4afd-8dfe-8a3b65fd9c2b)

## Connecting Superset with our Postgres Database

From your Superset homescreen, click on `Settings` --> `Database Connections` --> `Connect a Database` --> `PostgresSQL`
Fill in these details in the dialog modal:

![Screenshot 2023-06-07 at 10 22 27 PM](https://github.com/saadiahumayun/data-lake/assets/34272512/ba9fb2b7-9dd0-4027-b82b-c00d7a0df2d3)

Now that we're all set and connected, we're ready to roll and crunch some numbers!
Here are some screenshots of our analysis done on Superset!

![Screenshot 2023-06-07 at 10 28 17 PM](https://github.com/saadiahumayun/data-lake/assets/34272512/661ce436-ae45-41e4-b82b-774931ca3d7f) ![Screenshot 2023-06-07 at 10 29 08 PM](https://github.com/saadiahumayun/data-lake/assets/34272512/fad86980-d45c-4e9a-b624-9da1ad150aeb)





