# Airflow on Heroku

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/neovintage/airflow-button-test)

[Airflow](https://github.com/airbnb/airflow) is a framework to programmatically
author, schedule and monitor data workflows. Heroku is a great place to run
Airflow because you can focus on building the workflows without having to worry
about the infrastructure. As you start building more and more workflows, Heroku
will allow you to easily scale.

## Getting Started

This project is a reference for what your project should look like when you
deploy to Heroku. The easist way to start is to deploy the Project via the
Heroku Button. Here are all of the steps you need to handle to get the project
up and running:

* Generate a new fernet key. Fernet is used to encrypt the database connections
  stored in the metadata database. The new key will have to be saved in the config
  var `AIRFLOW_FERNET_KEY`:

  ```
$ python -c "from cryptography.fernet import Fernet; print Fernet.generate_key()"
pB_dxwr55GbGp0rKAU0LhfEGHx77CAlTRV6g5vvSZWI=
```

* The Procfile will need to change to include a scheduler and worker task like so:

  ```
web: airflow webserver -p $PORT
worker: airflow worker
scheduler: airflow scheduler
```
  Make sure that when you deploy these extra processes, you should only scale the
  scheduler to exactly one dyno.

* If you deployed via the Heroku Button the metadata database should be set up. If
  you didn't, then you'll need to log into a heroku bash session and instantiate
  the database. Make sure that a Heroku Postgres database has already been attached
  to the project:

  ```
$ heroku run bash
~ $ airflow initdb
```

* A new user will have to get created in the Airflow metadata database. Airflow
  has [great documentation](https://airflow.incubator.apache.org/security.html#web-authentication) on how to do this.

Once you get through the above steps, you're all set up and ready to go. Start
creating new DAGs!

## Best Practices

While Airflow does save connection information in the metadata database, when you build
your DAGs, you should environment variables to reference the connection to the
database where your data is saved. This example DAG shows you how you should connect
to your source system:

```
from airflow import DAG
from airflow.operators import PostgresOperator

dag = DAG(
        'transformation',
        default_args={ 'owner': 'airflow' })

task1 = PostgresOperator(
            sql='dim_get_count.sql',
            task_id='get_count',
            postgres_conn_id='DATABASE_URL',
            dag=dag)
```
