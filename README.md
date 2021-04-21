# Movies-ETL
Jupyter Notebooks for an Extract, Transform, Load (ETL) pipeline of movie data
files from web sources to a PostgreSQL database.

## Resources
- Data Source:
  - [wikipedia-movies.json](Resources/wikipedia-movies.json)
  - [Kaggle Movie Metadata and Ratings](https://www.kaggle.com/rounakbanik/the-movies-dataset/download)
- Software:
  - Python 3.7.6
  - pandas 1.0.1
  - NumPy 1.19.5
  - re
  - SQLAlchemy 1.3.13
  - PostgreSQL 11.10
  - pgAdmin 4 4.28.0
  - Jupyter Notebook 1.0.0

## Methods
### [ETL_function_test](ETL_function_test.ipynb)
This notebook defines the function `read_movie_data` which reads the data
files `wikipedia-movies.json`, `movies_metadata.csv`, and `ratings.csv` into
a separate pandas DataFrames which are then displayed to obtain a general
overview of the data at hand.

### [ETL_clean_wiki_movies](ETL_clean_wiki_movies.ipynb)
This notebook refactors the `read_movie_data` function defined in
`ELT_function_test.ipynb` to additionally clean the Wikipedia data. This
includes removing entries corresponding to TV shows, consolidating columns
containing similar data, removing columns with mostly null data, and
converting data types to their expected format. It then reads each of the
three files into pandas DataFrames that are again displayed to ensure
effective cleaning.

### [ETL_clean_kaggle_data](ETL_clean_kaggle_data.ipynb)
This notebook again refactors the `read_movie_data` function starting now from
that in `ETL_clean_wiki_movies.ipynb` to include cleaning the Kaggle movie
data and then merging with the previously cleaned Wikipedia data. We first
clean `kaggle_metadata.csv` which includes removing adult films, converting
data types to their expected format, and then merging with the Wikipedia data.
After merging, we consolidate columns from the Kaggle and Wikipedia datasets
which contain similar data. Finally, we clean the additional Kaggle data
`ratings.csv` to group ratings on a scale of one to five in increments of 0.5.
We then merge the ratings data with the previously merged Wikipedia and Kaggle
metadata and display the resulting DataFrames.

### [ETL_create_database.ipynb](ETL_create_database.ipynb)
This final notebook again refactors the function `read_movie_data`, this time
iterating from that in `ETL_clean_kaggle_data.ipynb` and renaming to
`etl_movie_data` to highlight that it includes the full ETL pipeline. We now
load the resulting cleaned DataFrames to our PostreSQL server. This includes
opening a connection to the database using
[`sqlalchemy.create_engine`](https://docs.sqlalchemy.org/en/14/core/engines.html)
and then loading each DataFrame with
[`pandas.DataFrame.to_sql`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.to_sql.html).

## Results
This ETL pipeline results in two tables in our SQL database: `movies` which
includes the clean Wikipedia and Kaggle metadata datasets along with `ratings`
which includes the original Kaggle data file `ratings.csv`. We include the
original `ratings.csv` data since this is already clean and our transform step
in the ETL pipeline only included binning ratings for each film. Loading this
data, we query each database to obtain the count of each:

- [movies](Images/movies_query.png) - 6,075 entries
- [ratings](Images/ratings_query.png) - 26,024,289 entries

## Usage
All code is contained in this repository's Jupyter Notebook files. Therefore
to replicate the results of this analysis, clone this repository and install
necessary dependencies into an isolated `conda` environment using the command:
```
conda env create -f environment.yml
```
Next, download and install `PostgreSQL` and the `pgAdmin4` interface by
selecting the appropriate package
[here](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads).
After installation, open `pgAdmin4` and create the database to store the movie
data:

1. Right-click "Servers > PostgreSQL 11" followed by "Create > Database...".
2. Name the database `movie_data`, and select "Save".

One can then open any of the included Jupyter Notebook files and run all
cells. The file [`Movies-ETL.ipynb`](Movies-ETL.ipynb) contains exploratory
analysis of the Wikipedia and Kaggle datasets to determine an effective
strategy to clean, merge, and load the data into our database. This strategy
is then implemented and refactored in the files listed above, resulting in the
function `etl_movie_data` in the notebook
[`ETL_create_database.ipynb`](ETL_create_database.ipynb). Running all cells in
this notebook in turn reads all movie data, cleans and merges the Wikipedia and
Kaggle data sets, and opens the connection to the local PostgreSQL database for
data loading. The last step can then be verified by returning to `pgAdmin4`,
opening the "Query Tool..." for the `movie_data` database (right-click
"PostgreSQL 11 > Databases > movie_data > Schemas > public > Tables" and select
"Query Tool..."), and running the following queries:
```
SELECT * FROM movies
SELECT * FROM ratings
```
