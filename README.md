# postgresql_wrapper
A simple wrapper of psycopg2

```python
import os

from .connection import Connection


config = {
    'host': os.getenv('POSTGRESQL_HOST'),
    'username': os.getenv('POSTGRESQL_USER'),
    'password': os.getenv('POSTGRESQL_PASSWD'),
    'dbname': os.getenv('POSTGRESQL_DB'),
    'port': int(os.getenv('POSTGRESQL_PORT')),
}
connection = Connection.load_connection(config)

# Execute SQL queries
query = 'CREATE TABLE "testing_table" ("key", "value");'
connection.execute(query)
query = '''\
INSERT INTO "testing_table" ("key", "value")
VALUES ('testing_key', 'testing_value');\
'''
connection.execute(query)
query = 'SELECT "key", "value" FROM "testing_table";'
keys = ('key', 'value')
for item in connection.query(query, keys):
    print(item)

# Bulk insert/delete/update
table = connection.load_table('testing_table')
bulk_insert = table.insert_each(10)
bulk_insert.send(None)  # Start bulk insert
for item in [{'key': f'key_{i}', 'value': f'value_{i}'} for i in range(30)]:
    bulk_insert.send(item)
bulk_insert.send(None)  # Finish
query = 'SELECT "key", "value" FROM "testing_table";'
keys = ('key', 'value')
for item in connection.query(query, keys):
    print(item)
```