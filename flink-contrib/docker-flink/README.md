Apache Flink cluster deployment on docker using docker-compose

# Installation

Install the most recent stable version of docker
https://docs.docker.com/installation/

Install the most recent stable version of docker-compose
https://docs.docker.com/compose/install/

# Build

Images are based on the official Java Alpine (OpenJDK 8) image. If you want to
build the flink image run:

    sh build.sh

or

    docker build -t flink .

If you want to build the container for a specific version of flink/hadoop/scala
you can configure it in the respective args:

    docker build --build-arg FLINK_VERSION=1.0.3 --build-arg HADOOP_VERSION=26 --build-arg SCALA_VERSION=2.10 -t "flink:1.0.3-hadoop2.6-scala_2.10" flink

# Deploy

- Deploy cluster and see config/setup log output (best run in a screen session)

        docker-compose up

- Deploy as a daemon (and return)

        docker-compose up -d

- Scale the cluster up or down to *N* TaskManagers

        docker-compose scale taskmanager=<N>

- Access the Job Manager container

        docker exec -it $(docker ps --filter name=flink_jobmanager --format={{.ID}}) /bin/sh

- Kill the cluster

        docker-compose kill

- Upload jar to the cluster

        docker cp <your_jar> $(docker ps --filter name=flink_jobmanager --format={{.ID}}):/<your_path>

- Copy file to all the nodes in the cluster

        for i in $(docker ps --filter name=flink --format={{.ID}}); do
            docker cp <your_file> $i:/<your_path>
        done

- Run a topology

From the jobmanager:

        docker exec -it $(docker ps --filter name=flink_jobmanager --format={{.ID}}) flink run -m <jobmanager:port> -c <your_class> <your_jar> <your_params>

If you have a local flink installation:

        $FLINK_HOME/bin/flink run -m <jobmanager:port> <your_jar>

or

        $FLINK_HOME/bin/flink run -m <jobmanager:port> -c <your_class> <your_jar> <your_params>

### Ports

- The Web Client is on port `48081`
- JobManager RPC port `6123` (default, not exposed to host)
- TaskManagers RPC port `6122` (default, not exposed to host)
- TaskManagers Data port `6121` (default, not exposed to host)

Edit the `docker-compose.yml` file to edit port settings.
