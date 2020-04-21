# Project Data Modeling with PostgreSQL

## Context

*A startup called Sparkify wants to analyze the data they've been collecting on songs and user activity on their new music streaming app. The analytics team is particularly interested in understanding what songs users are listening to. Currently, they don't have an easy way to query their data, which resides in a directory of JSON logs on user activity on the app, as well as a directory with JSON metadata on the songs in their app.*

*They'd like a data engineer to create a Postgres database with tables designed to optimize queries on song play analysis, and bring you on the project. Your role is to create a database schema and ETL pipeline for this analysis. You'll be able to test your database and ETL pipeline by running queries given to you by the analytics team from Sparkify and compare your results with their expected results.*

## Project Description

*In this project, you'll apply what you've learned on data modeling with Postgres and build an ETL pipeline using Python. To complete the project, you will need to define fact and dimension tables for a star schema for a particular analytic focus, and write an ETL pipeline that transfers data from files in two local directories i nto these tables in Postgres using Python and SQL.*

## Data

### Data
- **Song datasets**: all json files are nested in subdirectories under */data/song_data*. A sample of this files is:

```python
{"num_songs": 1, "artist_id": "ARJIE2Y1187B994AB7", "artist_latitude": null, "artist_longitude": null, "artist_location": "", "artist_name": "Line Renaud", "song_id": "SOUPIRU12A6D4FA1E1", "title": "Der Kleine Dompfaff", "duration": 152.92036, "year": 0}
```

- **Log datasets**: all json files are nested in subdirectories under */data/log_data*. A sample of a single row of each files is:

```python
{"artist":"Slipknot","auth":"LoggedIn","firstName":"Aiden","gender":"M","itemInSession":0,"lastName":"Ramirez","length":192.57424,"level":"paid","location":"New York-Newark-Jersey City, NY-NJ-PA","method":"PUT","page":"NextSong","registration":1540283578796.0,"sessionId":19,"song":"Opium Of The People (Album Version)","status":200,"ts":1541639510796,"userAgent":"\"Mozilla\/5.0 (Windows NT 6.1) AppleWebKit\/537.36 (KHTML, like Gecko) Chrome\/36.0.1985.143 Safari\/537.36\"","userId":"20"}
```

## Database Schema

**The schema used for this exercise is the Star Schema:**
*There is one main **fact table** containing all the measures associated to each **event (user song plays)**, and **4 dimentional tables**, each with a **primary key** that is being **referenced** from the **fact table**.*

### Fact Table

**Table songplays**

| COLUMN    | TYPE     | CONSTRAINT     |
|---   |---   |---   | 
|   songplay_id| SERIAL  |   PRIMARY KEY| 
|   start_time|   bigin|   NOT NULL| 
|   user_id|   int|   NOT NULL| 
|   level|   varchar |   | 
|   song_id|   varchar|   | 
|   artist_id|   varchar|   | 
|   session_id|   int|   | 
|   location|   text|   | 
|   user_agent|   text|   | 

### Dimensions Tables
 
 **Table users**
 
 | COLUMN  | TYPE  | CONSTRAINT  |
|---|---|---|
|   user_id| int|   PRIMARY KEY| 
|   first_name|   varchar|  | 
|   last_name|   varchar|  | 
|   gender|   varchar(1) |   | 
|   level|   varchar|   | 

**Table songs**

 | COLUMN  | TYPE  | CONSTRAINT   |
|---|---|---|
|   song_id| varchar  |   PRIMARY KEY| 
|   title|   text|  | 
|   artist_id|   varchar|   | 
|   year|   int |   | 
|   duration|   numeric|   |

**Table artists**

 | COLUMN  | TYPE  | CONSTRAINT   |
|---|---|---|
|   artist_id| varchar  |   PRIMARY KEY| 
|   name |   varchar |    | 
|   location |   text |    | 
|   latitude |   decimal |    | 
|   longitude |   decimal |    | 

**Table time**
 
 | COLUMN   | TYPE   | CONSTRAINT    |
|--- |--- |--- | 
|   start_time | bigint   |   PRIMARY KEY | 
|   hour  |   int  |    | 
|   day  |   int  |    | 
|   week  |   int   |       | 
|   month   |   int |       | 
|   year    |   int |       | 
|   weekday |   varchar |     |

## ETL Pipeline

The ETL is in the file **etl.py** and is divided in the next sections:

1. Connect to the database.
2. Process **song files**.
    1. Insert song data into **songs** table. 
    2. Insert artist data into **artists** table. 
3. Process **log_files**.
    1. Insert ts (unix timestamp) in **time** table.
        1. from the field **ts** we can extract year, day, hour, week, month and day of the week.
    2. Insert user info in **users** table.
    3. Insert songpplay records into **songplays** table. In this case we need an additional select to get the 
    artist_id and the artist_id. This is very important for the star schema will successful. I've improved this 
    query using an additional INDEX in song table for the artist_id field to make the JOIN with artists table.
4. Disconnect and finish.


## Running the ETL

First you should create the PostgreSQL database structure, by Running the python file `create_tables.py`:
```
python create_tables.py
```

Then parse the logs files and put them into Database Tables by running `etl.py`:

```
python etl.py
```

You can check the Records of the Database using `test.ipynb`

```
test.ipynb
```

**Note:** Remember to rerun create_tables.py to reset your tables before each time you run this notebook `test.ipynb`.

---


