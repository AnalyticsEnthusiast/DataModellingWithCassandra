## Data Modelling with Apache Cassandra

**Name: Darren Foley**

**Email: darren.foley@ucdconnect.ie**

---------------------------------------

### Project Index

| FileName                | Description                                              |
|:-----------------------:|:--------------------------------------------------------:|
| event_data/             | Contains event file logs from user activity on Sparkify  |
| images/                 | Contains screenshots for project Documentation           |
| .gitignore              | .gitignore, lists files not to be included               |
| etl_cassandra.ipynb     | Python notebook with prototype etl source code           |
| README.md               | Documenation                                             |
| event_datafile_new.csv  | CSV file which contains data from event_data/ directory  |

### Introduction

<p>A startup called Sparkify wants to analyze the data they've been collecting on songs and user activity on their new music streaming app. The analysis team is particularly interested in understanding what songs users are listening to. Currently, there is no easy way to query the data to generate the results, since the data reside in a directory of CSV files on user activity on the app.</p>

<p>They'd like a data engineer to create an Apache Cassandra database which can create queries on song play data to answer the questions, and wish to bring you on the project. Your role is to create a database for this analysis. You'll be able to test your database by running queries given to you by the analytics team from Sparkify to create the results.</p>


### Table Design

<p>In order to design tables on Apache Cassandra, you must first start with the queries that will run against the data and work back. The following three queries will be run against the data:</p>

1. Return the artist, song title and song's length in the music app history that was heard during sessionId = 338, and itemInSession = 4

2. Return only the following: name of artist, song (sorted by itemInSession) and user (first and last name) for userid = 10, sessionid = 182

3. Return every user name (first and last) in my music app history who listened to the song 'All Hands Against His Own'


For query 1, there is no requirement for a clustering column so just a simple composite key (session_id & item_in_session) is sufficient.

```
CREATE TABLE IF NOT EXISTS artist_song_session (
    session_id int,
    item_in_session int,
    artist text,
    first_name text,
    gender text,
    last_name text,
    length float,
    level text,
    location text,
    song text,
    user_id int,
    PRIMARY KEY (session_id, item_in_session)
    );
```


For query 2, we are asked to filter by user_id then session_id so they should come first in that order. Then we are asked to order by item_in_session which could benefit from being added as a clustering column. 

```
CREATE TABLE IF NOT EXISTS song_playlist_session (
    user_id int,
    session_id int,
    item_in_session int,
    artist text,
    first_name text,
    gender text,
    last_name text,
    length float,
    level text,
    location text,
    song text,
    PRIMARY KEY ((user_id, session_id), item_in_session)
    );
```

For query 3, we are asked to filter by song so that should come first. Adding user_id should be sufficent for providing a unique primary key. Only a single filter in the where clause so no need for a clustering column.

```
CREATE TABLE IF NOT EXISTS song_list_of_users (
    song text,
    user_id int,
    artist text,
    first_name text,
    gender text,
    item_in_session int,
    last_name text,
    length float,
    level text,
    location text,
    session_id int,
    PRIMARY KEY (song, user_id)
    );
```

If you need more detail, check out etl_cassandra.ipynb in the repository.
