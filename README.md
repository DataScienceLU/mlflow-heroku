
# :cyclone:	MLflow with basic authentication

This project is primarly a [soundsensing/mlflow-easyauth](https://github.com/soundsensing/mlflow-easyauth) fork.

Standard [MLflow](https://mlflow.org/) does not have any authentication for the web-interface.
This project adds basic HTTP authentication with a single username, password to the web interface.
And packages this up in a easy-to-install Docker image.

Primarily for use on Heroku with using Google Cloud Storage as the artifact store,
and Heroku Postgres as the tracking store.
It should be easy to make work on other Docker providers,
with other supported mlflow backends for artifacts and database.
Pull requests are welcome to fix any compatibility issues.

## :triangular_ruler: Architecture 

Whereas the original repo was achieving a [MLflow Scenario 4 tracking architecture], this repo aims to build a [Scenario 5]

![test](https://www.mlflow.org/docs/latest/_images/scenario_5.png)

# :rocket: Quickstart

## :card_file_box: Enable artifact persistence

Using [Google Cloud Storage](https://cloud.google.com/storage/).

Create a new or find an existing Google Cloud Storage bucket.

Create a Service Account for API credentials. Download the credentials JSON file.

Add the Service Account to Permissions on the bucket.
It needs to have the following roles:
```
Storage Legacy Bucket Writer
Legacy Bucket Reader
Legacy Object Reader
```

When deploying you will be asked to enter
```
ARTIFACT_URL=gs://<my-bucker/some/folder>
GOOGLE_APPLICATION_CREDENTIALS_JSON=<information-in-your-sa-json>
```

## :arrow_up: Deploying to Heroku

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy)

This will provision an Heroku app, and a Postgres add-on for persisting metrics etc.

## :mag_right: Use in MLflow client

Assuming that you have [mlflow tracking integration](https://www.mlflow.org/docs/latest/quickstart.html#using-the-tracking-api) set up already.

Configure the client

```
export MLFLOW_TRACKING_URI=https://<my-mlflow-instance>.herokuapp.com
export MLFLOW_TRACKING_USERNAME=<usersame>
export MLFLOW_TRACKING_PASSWORD=<password>
```

Create a new experiment

```
mlflow experiments create -n test
export MLFLOW_EXPERIMENT_NAME=test
```

Run your

```
python3 example.py
```

Open the web browser at your newly deployed Heroku app.
You should now have runs tracked with metrics being logged.

# :bulb: Tips & Tricks

## :zzz: Waking up free Heroku dynos

If you are using Heroku Free dynos, they will go to sleep after inactivity,
and then wake up again.
Thus when the mlflow client connects the app may be sleeping,
causing a the communication timeout and failing the ML pipeline.
If using this in automated workflows, it may be smart to wakeup the server
a bit in advance by making an HTTP request to it.
For example before installing dependencies of the project, etc.


[MLflow Scenario 4 tracking architecture]: https://www.mlflow.org/docs/latest/tracking.html#scenario-4-mlflow-with-remote-tracking-server-backend-and-artifact-stores
[Scenario 5]: https://www.mlflow.org/docs/latest/tracking.html#scenario-5-mlflow-tracking-server-enabled-with-proxied-artifact-storage-access