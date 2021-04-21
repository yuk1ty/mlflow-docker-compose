# On-premises Configuration Example

You can quickly setup your MLflow on-premises environment with this example.

 [Batteries Included](https://www.python.org/dev/peps/pep-0206/#batteries-included-philosophy), settings files are basically all done.

## Quickstart

1. Install docker, then install MLflow and hydra.

    ```sh
    pip install mlflow hydra-core
    ```

2. Make a folder to store artifacts.

    Edit `.env` if you want to change folder. (`/tmp/artifacts` by default. "artifacts" will be stored there the directory.)

    ```sh
    mkdir /tmp/artifacts
    ```

3. Get your mlflow server up and running. This takes time.

    ```sh
    docker-compose up --build -d
    ```

4. Confirm your server is running properly.

    Open server URI by `http://localhost:5000/`.

    Now let's run an example, this will run for all convinations of `alpha` and `l1_ratio` parameters.

    ```sh
    cd example
    python example_elasticnet_wine.py alpha=0.2,0.3,0.4,0.5,0.6,0.7 l1_ratio=0.4,0.5,0.6 -m
    ```

    If it runs successfully, reload browser and click on `sample_elasticnet_wine`:

    ![result image](images/on_pre_1.png)

### Where are your artifacts on browser?

If you click on one of run, deteil will open. And you can also find artifacts at the bottom of the page.

![result image](images/on_pre_2.png)

## Basic design

- User ID/password are basically fixed, these are used only for postgresql.
- Port is set to 5000 by default.
- Artifact folder is a little tricky, it has to be the same pathname between on the local environment and on the server running on docker. So it's set to `/tmp/artifacts` by default. This is kind of workaround to follow behavior of MLflow.

Find settings in `.env` if you need to change.

- On the client (ML application) side, `ExperimentRecorder` class wraps things essentially needed: environment variables, starting MLflow and etc.
- The `example/example_elasticnet_wine.py` explains how you can use in your ML apps.

## Trouble shooting

Stop containers first.

```sh
docker-compose down
```

See what's happening by running without `-d`.

```sh
docker-compose up
```

You might see some errors, check them and fix...

## Cleaning docker-created-files

Followings will clean up both containers/images.

```sh
docker ps -aq |xargs docker rm
docker images -aq |xargs docker rmi
```

Following will clean up cache.

```sh
docker system prune -a
```

## Caveat

- We're encountering a bug (https://github.com/mlflow/mlflow/issues/4208) on MLflow 1.15.0 so that we should install a fixed-versioning MLflow server to boot the server correctly. The bug is planning to be fixed in 1.15.1, but not released yet.
