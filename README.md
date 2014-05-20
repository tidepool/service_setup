# Getting the services up and running

This readme describes how to get the server backend of Tidepool setup on your own machine. When you are done setting up you will have the following services running on your machine:

* Postgres DB: Running on port 49169 on localhost. There will be 2 databases created - ```tidepool_V2_games_dev and ```tidepool_V2_users_dev. To connect to it, you can use: (password is docker)

    psql -h localhost -p 49169 -d tidepool_V2_games_dev -U docker --password

* Redis DB: Running on port 49170 on localhost. 

* user-service: Running on port 49172 on localhost. 

* game-service: Running on port 49171 on localhost.

## Installing the Helper script

There is a helper script to run service in this repository. It is called docker_run. You need to copy this script to somewhere that is in your path, say ~/bin folder if you have one. Once copied make sure to make it so it is executable:

    chmod +x docker_run

## Installing Docker

Use the below instructions to install Docker. (Homebrew install is the recommended one over installing manually. I.e unless you know what you are doing, use the Homebrew install.)

[http://docs.docker.io/installation/mac/](http://docs.docker.io/installation/mac/)

Once boot2docker and docker client are installed, first initialize boot2docker:

    boot2docker init

then make sure to map the following ports using the below script: 

    for i in {49000..49900}; do
     VBoxManage modifyvm "boot2docker-vm" --natpf1 "tcp-port$i,tcp,,$i,,$i";
     VBoxManage modifyvm "boot2docker-vm" --natpf1 "udp-port$i,udp,,$i,,$i";
    done

Now you are ready to bootup docker:
  
    boot2docker up

## Installing Postgres Container

We use Postgres as our main database. You will first need to install that:

    docker build -t tidepool/db github.com/tidepool/docker_postgres

## Installing Redis Container

We also use Redis for various temporary data storage. You will also need to install that:

    docker build -t tidepool/redis github.com/tidepool/docker_redis

Once it is built, you can run it using the included helper script here:

    docker_run redis 

## Installing Services (user-service and game-service)

In order to install the services on your local machine:

    docker build -t tidepool/user-service github.com/tidepool/user_service
    docker build -t tidepool/game-service github.com/tidepool/game_service

Once installed you can run them using: (in separate terminal tabs)

    docker_run user-service
    docker_run game-service

## Starting the backend 

The below need to run in separate tabs in your terminal. So in your terminal window, open 4 tabs and run each in one tab: (make sure docker_run is in your path)

First start Postgres DB:

    docker_run db 

Then start Redis:

    docker_run redis 

Start user-service: (will be locally available at: (http://localhost:49172))

    docker_run user-service

Start game-service: (will be locally available at: (http://localhost:49171))

    docker_run game-service

## Export the services from a publicly reachable URL

The below services are available from the localhost. But if you need to access them through a public URL then use ngrok:

    https://ngrok.com/download

Once downloaded simply run :

    ngrok 49172 

and 

    ngrok 49171 


