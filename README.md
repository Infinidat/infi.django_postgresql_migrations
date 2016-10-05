# Overview

This project provides custom Django migration operations for taking advantage of PostgreSQL-specific features.
It supports Django 1.7 and later.


# Usage

The custom migration operations can be used by creating an empty migration and then filling the operations list. For example:

```python
from django.db import migrations
from infi.django_postgresql_migrations.operations import CreateCompactIndex

class Migration(migrations.Migration):

    dependencies = [
        ('sample_app', '0001_initial'),
    ]

    operations = [
        CreateCompactIndex('SampleModel', 'sample_field'),
    ]
```


## CreateCompactIndex

An operation that creates a BTree GIN index. This kind of index is useful
when the column contains many repeating values, since it results in a much
smaller index size relative to a regular BTree index.

* See http://www.postgresql.org/docs/9.4/static/btree-gin.html
* See http://leopard.in.ua/2015/04/13/postgresql-indexes/

The constructor expects the model name, the field name, and an optional index name.
```python
CreateCompactIndex('SampleModel', 'sample_field')
```

It is possible to create a partial index by supplying a WHERE clause:
```python
CreateCompactIndex('SampleModel', 'sample_field', where='sample_field IS NOT NULL')
```


## CreateTrigramIndex

An operation that creates a trigram index. This kind of index is useful
for substring matching and fuzzy searches.

* See http://www.postgresql.org/docs/9.4/static/pgtrgm.html

The constructor expects the model name, the field name, and an optional index name.
```python
CreateTrigramIndex('SampleModel', 'sample_field')
```

It is possible to create a partial index by supplying a WHERE clause:
```python
CreateTrigramIndex('SampleModel', 'sample_field', where='sample_field IS NOT NULL')
```


## SetStatistics

An operation that sets the statistics target on a column. This controls how much
information about the column's values will be available for the query planner.

* See http://www.postgresql.org/docs/9.4/static/planner-stats.html

The constructor expects the model name, the field name, and an optional statistics target (defaults to 1000).
```python
SetStatistics('SampleModel', 'sample_field', target=500)
```


# Building the Project

Run the following commands:

    easy_install -U infi.projector
    projector devenv build

To run the tests use:

    bin/python src/tests/sample_project/manage.py test tests.sample_project

