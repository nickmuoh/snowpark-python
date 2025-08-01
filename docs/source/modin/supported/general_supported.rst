General utilities supported APIs
=======================================

The following table is structured as follows: The first column contains the method name.
The second column is a flag for whether or not there is an implementation in Snowpark for
the method in the left column.

.. note::
    ``Y`` stands for yes, i.e., supports distributed implementation, ``N`` stands for no and API simply errors out,
    ``P`` stands for partial (meaning some parameters may not be supported yet), and ``D`` stands for defaults to single
    node pandas execution via UDF/Sproc.

Data manipulations

+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| Method                      | Snowpark implemented? (Y/N/P/D) | Missing parameters               | Notes for current implementation                   |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``concat``                  | P                               | ``levels`` is not supported,     |                                                    |
|                             |                                 | ``copy`` is ignored              |                                                    |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``crosstab``                | P                               |                                  | ``N`` if ``aggfunc`` is not a `supported           |
|                             |                                 |                                  | aggregation function <agg_supp.html>`_,            |
|                             |                                 |                                  | margins is True, normalize is "all" or True,       |
|                             |                                 |                                  | and values is passed.                              |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``cut``                     | P                               | ``retbins``, ``labels``          | ``N`` if ``retbins=True``or ``labels!=False``      |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``__dataframe__``           | P                               |                                  | ``N`` for columns of type ``Timedelta`` and columns|
|                             |                                 |                                  | containing list objects                            |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``factorize``               | N                               |                                  |                                                    |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``from_dummies``            | N                               |                                  |                                                    |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``get_dummies``             | P                               | ``sparse`` is ignored            | ``Y`` if params ``dummy_na``, ``drop_first``       |
|                             |                                 |                                  | and ``dtype`` are default, otherwise ``N``         |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``json_normalize``          | Y                               |                                  |                                                    |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``lreshape``                | N                               |                                  |                                                    |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``melt``                    | P                               | ``col_level``, ``ignore_index``  | ``N`` if df.columns is a MultiIndex                |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``merge``                   | P                               | ``validate``                     | ``N`` if param ``validate`` is given               |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``merge_asof``              | P                               | ``suffixes``, ``tolerance``      | ``N`` if param ``direction`` is ``nearest``        |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``merge_ordered``           | N                               |                                  |                                                    |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``pivot``                   | P                               |                                  | See ``pivot_table``                                |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``pivot_table``             | P                               | ``observed``, ``margins``,       | ``N`` if ``index``, ``columns``, or ``values`` is  |
|                             |                                 | ``sort``                         | not str; or MultiIndex; or any ``aggfunc`` is not a|
|                             |                                 |                                  | `supported aggregation function <agg_supp.html>`_  |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``qcut``                    | P                               |                                  | ``N`` if ``labels!=False`` or ``retbins=True``.    |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``read_pickle``             | Y                               |                                  | Uses native pandas for reading.                    | 
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``read_csv``                | P                               |                                  | Reads both local and staged file(s) into a Snowpark|
|                             |                                 |                                  | pandas DataFrame. Note, the order of rows in the   |
|                             |                                 |                                  | may differ from the order of rows in the original  |
|                             |                                 |                                  | file(s) if using staged csvs.                      |
|                             |                                 |                                  |                                                    |
|                             |                                 |                                  | Local files are parsed with native pandas and thus |
|                             |                                 |                                  | support most of the parameters supported by pandas |
|                             |                                 |                                  | itself. The ``usecols`` and ``names`` parameter are|
|                             |                                 |                                  | applied after creating a temp table in snowflake.  |
|                             |                                 |                                  |                                                    |
|                             |                                 |                                  | Previously staged files will use the Snowflake     |
|                             |                                 |                                  | ``COPY FROM`` parser and schema inference. If you  |
|                             |                                 |                                  | need to use staged files often, it is recommended  |
|                             |                                 |                                  | that you upload these as parquet files to improve  |
|                             |                                 |                                  | performance. You can force the use of the Snowflake|
|                             |                                 |                                  | parser with ``engine=snowflake``                   |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``read_excel``              | Y                               |                                  | Uses native pandas to read excel files, using the  | 
|                             |                                 |                                  | engine specified by the pandas. You will need to   |
|                             |                                 |                                  | separately install a supported excel reader such   |
|                             |                                 |                                  | as openpyxl. Please refer to the native pandas     | 
|                             |                                 |                                  | `read excel`_ documentation for more details.      |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``read_json``               | P                               | ``orient``, ``typ``, ``dtype``,  | ``P``:                                             |
|                             |                                 | ``convert_axes``, ``lines``,     | - if ndjson files are passed                       |
|                             |                                 | ``convert_dates``, ``date_unit``,| - Supported parameters are ``compression`` and     |
|                             |                                 | ``keep_default_dates``,          | ``encoding``                                       |
|                             |                                 | ``encoding_errors``, ``nrows``,  |                                                    |
|                             |                                 | and ``chunksize`` will raise     |                                                    |
|                             |                                 | an error.                        |                                                    |
|                             |                                 | ``precise_float``, ``engine``,   |                                                    |
|                             |                                 | ``dtype_backend``, and           |                                                    |
|                             |                                 | ``storage_options`` are ignored. |                                                    |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``read_html``               | Y                               |                                  | Uses native pandas for reading.                    | 
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``read_xml``                | Y                               |                                  | Uses native pandas for reading.                    | 
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``read_feather``            | Y                               |                                  | Uses native pandas for reading.                    | 
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``read_parquet``            | P                               | ``use_nullable_dtypes``,         | Supported parameter(s) are: ``columns``            |
|                             |                                 | ``filesystem``, and ``filters``  |                                                    |
|                             |                                 | will raise an error if used.     |                                                    |
|                             |                                 | ``engine``, ``storage_options``, |                                                    |
|                             |                                 | ``dtype_backend``, and           |                                                    |
|                             |                                 | ``**kwargs`` are ignored.        |                                                    |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``read_snowflake``          | Y                               |                                  | Reading from tables as well as SELECT SQL Queries  |
|                             |                                 |                                  | supported, but ordering is not guaranteed for      |
|                             |                                 |                                  | SQL Queries that contain ORDER BY clauses. More    |
|                             |                                 |                                  | complex queries, including CTEs and CTEs with      |
|                             |                                 |                                  | anonymous stored procedures are also supported.    |
|                             |                                 |                                  | Obtaining results from stored procedures is also   |
|                             |                                 |                                  | supported via CALL queries.                        |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``read_sas``                | Y                               |                                  | Uses native pandas to read sas files.              | 
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``read_table``              | N                               |                                  |                                                    |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``to_pandas``               | Y                               |                                  |                                                    |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``to_snowflake``            | Y                               | ``**kwargs`` are currently       |                                                    |
|                             |                                 | ignored                          |                                                    |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``to_snowpark``             | Y                               |                                  | Convert the Snowpark pandas DataFrame or Series to |
|                             |                                 |                                  | a Snowpark DataFrame. Once converted to a Snowpark |
|                             |                                 |                                  | DataFrame, no ordering information will be         |
|                             |                                 |                                  | preserved.                                         |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``unique``                  | Y                               |                                  |                                                    |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``wide_to_long``            | N                               |                                  |                                                    |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+

Top-level dealing with missing data

+-----------------------------+---------------------------------+----------------------------------------------------+
| Method                      | Snowpark implemented? (Y/N/P/D) | Notes for current implementation                   |
+-----------------------------+---------------------------------+----------------------------------------------------+
| ``isna``                    | Y                               |                                                    |
+-----------------------------+---------------------------------+----------------------------------------------------+
| ``isnull``                  | Y                               |                                                    |
+-----------------------------+---------------------------------+----------------------------------------------------+
| ``notna``                   | Y                               |                                                    |
+-----------------------------+---------------------------------+----------------------------------------------------+
| ``notnull``                 | Y                               |                                                    |
+-----------------------------+---------------------------------+----------------------------------------------------+

Top-level dealing with numeric data

+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| Method                      | Snowpark implemented? (Y/N/P/D) | Missing parameters               | Notes for current implementation                   |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``to_numeric``              | P                               | ``downcast`` is ignored          | ``N`` if ``error == "ignore"``                     |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+

Top-level dealing with datetime-like data

+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| Method                      | Snowpark implemented? (Y/N/P/D) | Missing parameters               | Notes for current implementation                   |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``bdate_range``             | P                               |                                  | ``N`` for custom frequencies                       |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``date_range``              | P                               |                                  | ``N`` for custom frequencies                       |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``infer_freq``              | N                               |                                  |                                                    |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``period_range``            | N                               |                                  |                                                    |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``timedelta_range``         | N                               |                                  |                                                    |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``to_datetime``             | P                               | ``cache`` is ignored             | ``N``:                                             |
|                             |                                 |                                  | - if ``format`` is None or not supported in        |
|                             |                                 |                                  | Snowflake                                          |
|                             |                                 |                                  | - or if params ``exact``, ``infer_datetime_format``|
|                             |                                 |                                  | is given                                           |
|                             |                                 |                                  | - or ``origin == "julian"``                        |
|                             |                                 |                                  | - or ``arg`` is DataFrame and data type is not int |
|                             |                                 |                                  | - or ``arg`` is Series and data type is string     |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+
| ``to_timedelta``            | P                               | ``errors``                       | ``N`` if ``errors`` is given or converting from    |
|                             |                                 |                                  | string type                                        |
+-----------------------------+---------------------------------+----------------------------------+----------------------------------------------------+

Top-level dealing with Interval data

+---------------------------------------+---------------------------------+----------------------------------------------------+
| Method                                | Snowpark implemented? (Y/N/P/D) | Notes for current implementation                   |
+---------------------------------------+---------------------------------+----------------------------------------------------+
| ``interval_range``                    | N                               |                                                    |
+---------------------------------------+---------------------------------+----------------------------------------------------+

Top-level evaluation

+---------------------------------------+---------------------------------+----------------------------------------------------+
| Method                                | Snowpark implemented? (Y/N/P/D) | Notes for current implementation                   |
+---------------------------------------+---------------------------------+----------------------------------------------------+
| ``eval``                              | N                               |                                                    |
+---------------------------------------+---------------------------------+----------------------------------------------------+

Datetime formats

+---------------------------------------+---------------------------------+----------------------------------------------------+
| Method                                | Snowpark implemented? (Y/N/P/D) | Notes for current implementation                   |
+---------------------------------------+---------------------------------+----------------------------------------------------+
| ``tseries.api.guess_datetime_format`` | N                               |                                                    |
+---------------------------------------+---------------------------------+----------------------------------------------------+


Hashing

+---------------------------------------+---------------------------------+----------------------------------------------------+
| Method                                | Snowpark implemented? (Y/N/P/D) | Notes for current implementation                   |
+---------------------------------------+---------------------------------+----------------------------------------------------+
| ``util.hash_array``                   | N                               |                                                    |
+---------------------------------------+---------------------------------+----------------------------------------------------+
| ``util.hash_pandas_object``           | N                               |                                                    |
+---------------------------------------+---------------------------------+----------------------------------------------------+

Importing from other DataFrame libraries

+---------------------------------------+---------------------------------+----------------------------------------------------+
| Method                                | Snowpark implemented? (Y/N/P/D) | Notes for current implementation                   |
+---------------------------------------+---------------------------------+----------------------------------------------------+
| ``api.interchange.from_dataframe``    | N                               |                                                    |
+---------------------------------------+---------------------------------+----------------------------------------------------+

.. _read excel: https://pandas.pydata.org/docs/reference/api/pandas.read_excel.html
