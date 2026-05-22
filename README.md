#FastAPI + PySpark API on Databricks Delta Table

This project demonstrates how to build a REST API using FastAPI on top of a Delta table in Databricks with Apache Spark.

The API reads data from the following Delta table:

samples.nyctaxi.trips

The goal of the project is to expose Spark/Delta data through HTTP endpoints instead of querying data directly from notebooks.

Project Structure
FastAPI
   ↓
PySpark
   ↓
Delta Table
   ↓
Databricks

Components used in the project:

Component	Purpose
FastAPI	REST API framework
PySpark	Distributed data processing
Delta Lake	Table storage layer
Pydantic	Request/response schema validation
TestClient	API testing without external server
Pandas	Display API results in notebook
Delta Table Schema
CREATE TABLE samples.nyctaxi.trips (
  tpep_pickup_datetime TIMESTAMP,
  tpep_dropoff_datetime TIMESTAMP,
  trip_distance DOUBLE,
  fare_amount DOUBLE,
  pickup_zip INT,
  dropoff_zip INT
)
USING delta
API Endpoints
1. List Trips

Retrieve trips with optional filters.

Endpoint:

GET /trips

Query Parameters:

Parameter	Description
min_fare	Minimum fare amount
pickup_zip	Filter by pickup ZIP code
limit	Number of rows to return

Example:

GET /trips?min_fare=20&pickup_zip=10001&limit=5
2. Trip Statistics

Calculate aggregated metrics from the Delta table.

Endpoint:

GET /trips/stats

Returned Metrics:

Total trips
Average fare
Average trip distance
Maximum fare
3. Trips by Pickup ZIP

Retrieve trips for a specific pickup ZIP code.

Endpoint:

GET /trips/by-pickup/{pickup_zip}

Example:

GET /trips/by-pickup/10001
4. Top Pickup ZIP Codes

Identify the most frequent pickup ZIP codes.

Endpoint:

GET /trips/top-zips

This endpoint uses:

groupBy
count
orderBy

to aggregate Spark DataFrames.

Main Concepts Used

The project demonstrates:

FastAPI route creation
Query parameters
Path parameters
Spark DataFrame filtering
Aggregations with PySpark
Delta table access
Response serialization
Notebook-based API testing
Converting Spark DataFrames to JSON responses
Example Code Flow
Client Request
      ↓
FastAPI Endpoint
      ↓
Spark Query
      ↓
Delta Table
      ↓
JSON Response
Testing

The project uses FastAPI TestClient to test API endpoints directly inside a Databricks notebook.

Example:

client = TestClient(app)

r = client.get("/trips/stats")

print(r.json())
Sample Dataset

Example records from the Delta table:

pickup_datetime	dropoff_datetime	distance	fare	pickup_zip
2016-02-13 21:47:53	2016-02-13 21:57:15	1.4	8.0	10103
2016-02-06 19:40:58	2016-02-06 19:52:32	1.8	9.5	10001
