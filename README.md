# Movies-ETL
Jupyter Notebooks for ETL pipeline of movie data files from web sources to a PostgreSQL database.

## Resources
- Data Source:
  - [wikipedia-movies.json](Resources/wikipedia-movies.json)
  - [Kaggle Movie Metadata and Ratings](https://www.kaggle.com/rounakbanik/the-movies-dataset/download)

## Methods
### [ETL_function_test](ETL_function_test.ipynb)
This notebook defines the function `read_movie_data` which reads the data files `wikipedia-movies.json`, `movies_metadata.csv`, and `ratings.csv`
into a separate pandas DataFrames for each which are then displayed to obtain a general overview of the data at hand.

### [ETL_clean_wiki_movies](ETL_clean_wiki_movies.ipynb)
This notebook refactors the `read_movie_data` function defined in `ELT_function_test.ipynb` to additionally clean the wikipedia data. This cleaning
includes removing entries corresponding to TV shows, consolidating columns containing similar data, removing columns with mostly null data, and
converting data types to their expected format. It then reads each of the three files into pandas DataFrames that are again displayed to ensure
effective cleaning.

### [ETL_clean_kaggle_data](ETL_clean_kaggle_data.ipynb)
This notebook again refactors the `read_movie_data` function starting now from that in `ETL_clean_wiki_movies.ipynb` to include cleaning the Kaggle movie
data and then merging with the previously cleaned Wikipedia data.
