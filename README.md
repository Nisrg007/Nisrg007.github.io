# Nisrg007.github.io
21BCP105 For Cloud IA-2

In this blog post, we will be deploying a full-stack web application using Docker. The application will be split into three microservices.

1.Frontend Container of ReactJs
2.Backend Container of expressJs
3.Database Container of MongoDB

Before we dive into deployment, let's grasp the concept of Docker and its necessity. Docker serves as a platform enabling the development, testing, and deployment of applications within containers. These containers are compact, independent, and self-sufficient software packages encompassing all essentials for application execution: code, runtime, system utilities, libraries, and configurations. They operate in isolation from both each other and the underlying host, facilitating the uniform deployment of applications across varied environments without necessitating alterations. This ensures straightforward deployment in a reliable and repeatable manner.

Docker also provides tools for managing containers, such as Docker Compose, which allows you to define and run multi-container applications. In this blog, we will be using Docker Compose to define and run our full-stack web application. Let’s start

[1. Create a full stack application or use an existing one built on ReactJs, ExpressJs and MongoDB.](URL)
I have used an application of my own App or use any existing application you have. Just make sure it has the same tech-stack.

This is the file structure of our application:

GFG-HACK/
└── client/
└── API/

[2. Create a repository on Docker Hub to store your Docker images](URL).
Go to Docker Hub and create a new repository to store your Docker images. You can create a public or private repository depending on your requirements. Once you have created the repository, note down the repository name as we will need it later to push our Docker images to the repository.

This is what your repository should look like:
![image](https://github.com/Nisrg007/Nisrg007.github.io/assets/120625010/d752b967-576e-42fc-b66d-f9da4b9beab7)

[3. Create a Dockerfile for each of the services.](URL)
**create a Docker-compose file in your web app folder:**
docker-compose.yml

version: '3'

services:
  frontend:
    build:
      context: ./client
      dockerfile: Dockerfile
    ports:
      - 3000:3000
    depends_on:
      - backend
    networks:
      - my_network
    volumes:
      - ./client:/app/frontend

  backend:
    build:
      context: ./api
      dockerfile: Dockerfile
    ports:
      - 4000:4000
    depends_on:
      - mongodb
    networks:
      - my_network
    volumes:
      - ./api:/app/backend

  mongodb:
    image: mongo
    ports:
      - 27017:27017
    networks:
      - my_network
    volumes:
      - mongo-data:/data/db

networks:
  my_network:
    driver: bridge

volumes:
  mongo-data:

 **1.Now, change your current working directory to api**
  cd api
  **2.create a Dockerfile in the api folder**
  touch Dockerfile

FROM node:lts-iron
WORKDIR /app/frontend
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "run", "start"]

Dockerfile is a text file that contains a set of instructions that are used to build a Docker image. The Docker image is a lightweight, standalone, and executable package of software that includes everything needed to run an application. The Dockerfile contains instructions to build the image, such as the base image, working directory, dependencies, and commands to run the application.

**3.1 Dockerfile for ReactJs server at path client/Dockerfile**
Before creating the Dockerfile for the ReactJs server, make sure you have a build script in your package.json file. If not, add the following script to your package.json file:

  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },

  **1.Now, change your current working directory to client**
  cd client
  **2.create a Dockerfile in the client folder**
  touch Dockerfile

**Open the Dockerfile in a text editor of your choice and follow the below steps:**
# syntax=docker/dockerfile:1

ARG NODE_VERSION=20.10.0

FROM node:${NODE_VERSION} as base
WORKDIR /app/backend
EXPOSE 4000

FROM base as dev
RUN --mount=type=bind,source=package.json,target=package.json \
    --mount=type=bind,source=package-lock.json,target=package-lock.json \
    --mount=type=cache,target=/root/.npm \
    npm ci --include=dev
USER node
COPY . .
CMD npm run dev

FROM base as prod
ENV NODE_ENV development
RUN --mount=type=bind,source=package.json,target=package.json \
    --mount=type=bind,source=package-lock.json,target=package-lock.json \
    --mount=type=cache,target=/root/.npm \
    npm ci --omit=dev
USER node
COPY . .
CMD node index.js

**Now we will build the Docker image from the created Dockerfile for the server and push it to the Docker Hub repository, follow the below steps carefully:**
~Now we will build the Docker image from the created Dockerfile for the server and push it to the Docker Hub repository, follow the below steps carefully
 -docker build -t <DockerHubUsername>/<RepositoryName>:server 
 

![image](https://github.com/Nisrg007/Nisrg007.github.io/assets/120625010/7829cdc3-b2f6-4326-bdba-97becebb205d)

This command will push the Docker image to the Docker Hub repository. You can check the Docker Hub repository to see if the image has been pushed successfully.
frontend:
![image](https://github.com/Nisrg007/Nisrg007.github.io/assets/120625010/7b655c9d-10fa-42c3-92ac-4b745c29f09d)
backend:
![image](https://github.com/Nisrg007/Nisrg007.github.io/assets/120625010/547304a1-7f1a-4f26-a768-5dcafbaa55e6)


Now we have our client and server images pushed to the Docker Hub repository and ready to run. We now will pull an already created MongoDB image from the Docker Hub and run it.
**3.3 Pulling MongoDB image from Docker Hub**

**Pull the MongoDB image from the Docker Hub using the following command:**
![image](https://github.com/Nisrg007/Nisrg007.github.io/assets/120625010/534db306-ee83-478e-9f6b-e24ff0a42ff1)
**4. Run our application with the created docker images**
Now that we have all our images ready, we can run our application by running all the docker containers and exposing the ports on which they will run so all three microservices can communicate with each other.

Follow the below commands to run your containers:
docker run -dp 3000:3000 <DockerHubUsername>/<RepositoryName>:client
docker run -dp 27017:27017 mongo

Once all the containers are up and running, you can test the app at http://localhost:3000

This is how the website will look like:

![image](https://github.com/Nisrg007/Nisrg007.github.io/assets/120625010/569d88c6-d97a-4f33-bebe-d4501f7ab174)

conclusion:
In encapsulating your MERN stack application within Docker containers and harnessing Docker networks, you've attained a transportable, effective, and adaptable development ecosystem. This methodology enhances development processes, fosters collaboration, and sets the stage for effortless deployment to production environments.

However, this marks only the initial phase! Delve deeper into refining strategies for production settings, such as refining volume management for enduring data retention and incorporating container orchestration platforms like Kubernetes. As your MERN application evolves, Docker will persist as an indispensable resource in your developer arsenal.
We have successfully deployed our three-tier MERN stack application with docker!
