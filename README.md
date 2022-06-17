# Inconsistent behaviour for seeds depending on table type

This is a small demo of inconsistent behaviour of [dbt-databricks](https://github.com/databricks/dbt-databricks) for materializing seeds depending on table type. The issue is reported [here](https://github.com/databricks/dbt-databricks/issues/114).

When a seed is configured as managed table (by default) - the data is refreshed on every `dbt seed` run (behaviour comparable to models' `table` materialization), but when a seed is configured as external table (by setting `location_root`), the data is appended (behaviour comparable to models' `incremental` materialization with `append` strategy).

## Setup

    pip install -r requirments.txt


## Reproduce

Observe the different configuration of two seeds:

```yml
seeds:
  foo:
    cities:
    countries:
      location_root: "{{ env_var('LOCATION') }}"
```


Run dbt seed *twice*:

    HOST=<YOUR_HOST> CLUSTER=<YOUR_CLUSTER> TOKEN=<YOUR_TOKEN> LOCATION=<YOUR_BUCKET> dbt seed && dbt seed

Observe the content in both tables.


```sql
select * from foo.cities
```

|id|name|
|--|----|
|1|berlin|
|2|paris|



```sql
select * from foo.countries
```

|id|name|
|--|----|
|1|germany|
|1|germany|
|2|france|
|2|france|
