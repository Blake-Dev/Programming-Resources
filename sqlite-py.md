# SQLite3 using Python
SQLite Python API Reference Guide

---

### Connecting to a DB
First, let's connect to the DB.
The `db_file` parameter passed in will be the path to your DB.
```
import sqlite3
from sqlite3 import Error

def create_connection(db_file):
    try:
        conn = sqlite3.connect(db_file)
        return conn 
        #return a conn object if it successfully connects
    except Error as e:
        print(e)
    
    return None
    #If there was error connecting to db, an error will be displayed to the user and there will be no connection object returned

```

### Creating a Table
Once connected, we will need to make a table. This will define how your data can be stored.

```
"""
@param create_table_sql: The query you will write to create the table
@param conn: The connection object created/returned from the function above
"""
def create_table(conn, create_table_sql):
    try:
        c = conn.cursor()
        c.execute(create_table_sql)
    except Error as e:
        print(e)
```

The above function will be used to create a table. The code below is how you will use the two functions you've written to create a table:

```
database = "C:\\present\\working\\directory\\mydb.db"

create_table_example = """CREATE TABLE IF NOT EXISTS users (
                            id integer PRIMARY KEY,
                            first_name text NOT NULL,
                            username text NOT NULL,
                            email text
);"""

#create db connection
conn = create_connection(database)

if conn is not None:
    #create users table
    create_table(conn, create_table_example)
else:
    print("Error: Cannot create db")

if __name__ == '__main__':
    main()
```