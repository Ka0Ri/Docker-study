# Docker-study
Studying Docker

When we create Docker containers, we need to use some tools and terminologies such as Dockerfile, Docker Images or Docker Hub.

Docker containers are running instances of Docker images.

Docker images contain all the tools, libraries, dependencies, executable application source code necessary to run the application as a container.

We can build the Docker image from common repositories or from scratch using a Dockerfile which is a text file containing instructions on how to build Docker container image

Docker Hub is the public repository of Docker images. We can think of it as a GitHub for Docker images

## First Things First: The Micro-services
To put machine learning into production, let’s consider that the application needs to be broken down into smaller micro-services such as ingestion, preparation, combination, separation, training, evaluation, inference, postprocessing and monitoring

After developing your model, it might run perfectly well in your laptop or server but not really on other systems such as when you move the model to the production stage or another server.

The other challenging situation is that our machine learning model can certainly be written with one single programming language such as python but the application will certainly need to interact with other applications written in other programming languages for data ingestion, data preparation, front-end, etc.

Docker allows to better manage all these interactions as each micro-service can be written in a different language allowing scalability and the easy addition or deletion of independent services

Docker allows to easily reproduce the working environment that is used to train and run the machine learning model anywhere.

Docker allows packaging the code and dependencies into containers that can be ported to different servers even if it’s a different hardware or operating system. A training model can be developed on a local machine and be easily ported to external clusters with additional resources such as GPUs, more memory or powerful CPUs

It’s easy to deploy and make your model available to the globe by wrapping it into an API in a container and deploy the container using technology such as OpenShift, a Kubernetes distribution

# Create Docker file
Dockerfile is simply a text-based script of instructions that is used to create a container image

Create a file named `Dockerfile` in the same folder

```
FROM node:12-alpine
RUN apk add --no-cache python g++ make
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "src/index.js"]
```
We instructed the builder that we wanted to start from the node:12-alpine image. After the image was downloaded, we copied in our application and used yarn to install our application’s dependencies. The CMD directive specifies the default command to run when starting a container from this image.


# Build Docker image

Now build the container image using the docker build command.

```
docker build -t getting-started .
```
the -t flag tags our image. Think of this simply as a human-readable name for the final image. Since we named the image getting-started, we can refer to that image when we run a container. The . at the end of the docker build command tells that Docker should look for the Dockerfile in the current directory.

# Start an app container

```
docker run -dp 3000:3000 getting-started
```

# Push Docker image

Login to the Docker Hub using the command 
```
docker login -u YOUR-USER-NAME
```

Use the `docker tag` command to give the getting-started image a new name. Be sure to swap out YOUR-USER-NAME with your Docker ID.
```
docker tag getting-started YOUR-USER-NAME/image-name
```

Now try your push command again. If you’re copying the value from Docker Hub, you can drop the `tagname` portion, as we didn’t add a tag to the image name. If you don’t specify a tag, Docker will use a tag called `latest`.

```
docker push YOUR-USER-NAME/image-name
```

# Pull and use Docker image

In the terminal, start your freshly pushed app.
```
docker run -dp 3000:3000 YOUR-USER-NAME/image-name
```
You should see the image get pulled down and eventually start up!

Or, you can pull a container by the command
```
docker pull YOUR-USER-NAME/<repo-name>:tag name
```

# Volume
Volumes provide the ability to connect specific filesystem paths of the container back to the host machine. If a directory in the container is mounted, changes in that directory are also seen on the host machine. If we mount that same directory across container restarts, we’d see the same files.

As mentioned, we are going to use a named volume. Think of a named volume as simply a bucket of data. Docker maintains the physical location on the disk and you only need to remember the name of the volume. Every time you use the volume, Docker will make sure the correct data is provided.

1. Create a volume by using the docker volume create command.
```
docker volume create todo-db
```
2. Start the todo app container, but add the -v flag to specify a volume mount. We will use the named volume and mount it to /etc/todos, which will capture all files created at the path.
```
docker run -dp 3000:3000 -v todo-db:/etc/todos getting-started
```
