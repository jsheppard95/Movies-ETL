# Movies-ETL
Jupyter Notebooks for an Extract, Transform, Load (ETL) pipeline of movie data files from web sources to a PostgreSQL database.

## Resources
- Data Source:
  - [wikipedia-movies.json](Resources/wikipedia-movies.json)
  - [Kaggle Movie Metadata and Ratings](https://www.kaggle.com/rounakbanik/the-movies-dataset/download)

## Methods
### [ETL_function_test](ETL_function_test.ipynb)
This notebook defines the function `read_movie_data` which reads the data files `wikipedia-movies.json`, `movies_metadata.csv`, and `ratings.csv`
into a separate pandas DataFrames which are then displayed to obtain a general overview of the data at hand.

### [ETL_clean_wiki_movies](ETL_clean_wiki_movies.ipynb)
This notebook refactors the `read_movie_data` function defined in `ELT_function_test.ipynb` to additionally clean the Wikipedia data. This
includes removing entries corresponding to TV shows, consolidating columns containing similar data, removing columns with mostly null data, and
converting data types to their expected format. It then reads each of the three files into pandas DataFrames that are again displayed to ensure
effective cleaning.

### [ETL_clean_kaggle_data](ETL_clean_kaggle_data.ipynb)
This notebook again refactors the `read_movie_data` function starting now from that in `ETL_clean_wiki_movies.ipynb` to include cleaning the Kaggle movie
data and then merging with the previously cleaned Wikipedia data. We first clean `kaggle_metadata.csv` which includes removing adult films, converting data
types to their expected format, and then merging with the Wikipedia data. After merging, we consolidate columns from the Kaggle and Wikipedia datasets which
contain similar data. Finally, we clean the additional Kaggle data `ratings.csv` to group ratings on a scale of one to five in increments of 0.5. We then
merge the ratings data with the previously merged Wikipedia and Kaggle metadata and display the resulting DataFrames.

### [ETL_create_database.ipynb](ETL_create_database.ipynb)
This final notebook again refactors the function `read_movie_data`, this time iterating from that in `ETL_clean_kaggle_data.ipynb` and renaming to
`etl_movie_data` to highlight that it includes the full ETL pipeline. We now load the resulting cleaned DataFrames to our PostreSQL server. This includes
opening a connection to the database using [`sqlalchemy.create_engine`](https://docs.sqlalchemy.org/en/14/core/engines.html) and then loading each DataFrame
with [`pandas.DataFrame.to_sql`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.to_sql.html).

## Results
This ETL pipeline results in two tables in our SQL database: `movies` which includes the clean Wikipedia and Kaggle metadata datasets along with `ratings`
which includes the original Kaggle data file `ratings.csv`. We include the original `ratings.csv` data since this is already clean and our
transform step in the ETL pipeline only included binning ratings for each film. Loading this data, we query each database to obtain the count of each:

- [movies](Resources/movies_query.png) - 6,075 entries
- [ratings](Resources/ratings_query.png) - 26,024,289 entries
