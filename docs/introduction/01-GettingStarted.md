# 1. Getting Started

## Launching the development environment

> 💡 Prerequisite: Do ensure you have [Docker](https://www.docker.com/get-started) installed on your local machine! Also, make sure that Docker desktop is running on the background!

1. Frontend: https://github.com/2023-Govtech-Geekout-GDS/frontend.git
2. Backend: https://github.com/2023-Govtech-Geekout-GDS/backend.git

Firstly, clone this repository and navigate into the main codebase

```console
mkdir eng-bootcamp-2022 &&
cd eng-bootcamp-2022 &&
git clone https://github.com/2023-Govtech-Geekout-GDS/frontend.git &&
git clone https://github.com/2023-Govtech-Geekout-GDS/backend.git
```

Checkout to the exercise branch in Backend and Frontend applications

> cd into directories backend and frontend and run the commands below


```console
git checkout checkpoint-0
```

Run the command below:

Install jest to run tests
```console
npm i --save-dev @types/jest
```

Start the servers
```console
docker-compose up --build
```

If running docker compose in detached mode instead (`docker-compose up --build --detach`), viewing the logs of a container is simple:

```console
docker logs frontend_frontend_1
```

```console
docker logs backend_backend_1
```

To tear down the environment:

```console
docker-compose down
```

If all is good, access the locally hosted front end app at `http://localhost:3000/`


![Frontend App](https://user-images.githubusercontent.com/43963814/134466840-341293c3-c0cd-4edd-b64d-e6564ab20199.png "Frontend App")

You should arrive at this screen