# Airflow on Heroku

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/neovintage/airflow-button-test)

[Airflow](https://github.com/airbnb/airflow) is a framework to programmatically
author, schedule and monitor data workflows. Deploying Airflow to Heroku brings
elasticity and asdfasd

## Getting Started

This project is a reference for what your project should look like when you
deploy to Heroku. The easist way to start is to deploy the Project via the
Heroku Button. Once deployed there are a few caveats:

* Authentication _doesnt_ exist as part of airflow right now. There are some
  pull requests to fix this but have yet to be deployed
* When you're ready to start deploying workflows, you'll need to switch the
  executor to `CeleryExecutor` in `airflow.cfg`. The Procfile will need to
  change to include a scheduler and worker task like so:
  ```
web: airflow webserver -p $PORT
worker: airflow worker
scheduler: airflow scheduler
```
