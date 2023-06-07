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
docker pull apache/airflow:2.2.3
~~~
~~~
docker pull bde2020/spark-master:3.2.0-hadoop3.2
~~~
~~~
docker pull bde2020/spark-worker:3.2.0-hadoop3.2
~~~
~~~
docker pull jupyter/pyspark-notebook:spark-3.2.0
~~~
~~~
docker pull postgres:9.5.3
~~~
~~~
docker pull bitnami/zookeeper:3.7.0
~~~
~~~
docker pull apache/nifi-registry:latest
~~~
~~~
docker pull apache/nifi:1.15.0
~~~
~~~
docker pull minio/minio
~~~
~~~
docker pull minio/mc
~~~
~~~
docker pull redis:latest

