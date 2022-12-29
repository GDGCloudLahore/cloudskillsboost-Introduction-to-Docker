![image](https://user-images.githubusercontent.com/71556060/210015814-ccb8ec29-67aa-4fde-a077-ab9e340db58e.png)

## What you'll learn

In this lab, you will learn how to do the following:
- How to build, run, and debug Docker containers.
- How to pull Docker images from Docker Hub and Google Artifact Registry.
- How to push Docker images to Google Artifact Registry.


### Prepare the ENV... 
**Activate Cloud Shell** 
```
project id = qwiklabs-gcp-00-cd8d380deef5
gcloud auth list
gcloud config set account qwiklabs-gcp-00-718a66edd301
gcloud config list project
```
### START THE LAB

---------------------TASK = 1---------------------------

**run a hello world container to get started:**
docker run hello-world
docker images
docker ps
docker ps -a


---------------------TASK = 2---------------------------
mkdir test && cd test
**create Dockerfile:**
```
cat > Dockerfile <<EOF
# Use an official Node runtime as the parent image
FROM node:lts
# Set the working directory in the container to /app
WORKDIR /app
# Copy the current directory contents into the container at /app
ADD . /app
# Make the container's port 80 available to the outside world
EXPOSE 80
# Run app.js using node when the container launches
CMD ["node", "app.js"]
EOF
```

**create the node application:**
```
cat > app.js <<EOF
const http = require('http');
const hostname = '0.0.0.0';
const port = 80;
const server = http.createServer((req, res) => {
    res.statusCode = 200;
    res.setHeader('Content-Type', 'text/plain');
    res.end('Hello World\n');
});
server.listen(port, hostname, () => {
    console.log('Server running at http://%s:%s/', hostname, port);
});
process.on('SIGINT', function() {
    console.log('Caught interrupt signal and will exit');
    process.exit();
});
EOF
```

**Now build the image:**
docker build -t node-app:0.1 .
docker images

---------------------TASK = 3---------------------------

**run container on 4000**
```
docker run -p 4000:80 --name my-app node-app:0.1

```
**preview on localhost**
```
curl http://localhost:4000
```
**top and remove the container:**
```
docker stop my-app && docker rm my-app
```
**Now run the container in the background:**
```
docker run -p 4000:80 --name my-app -d node-app:0.1
docker ps

```
**logs**

```
docker logs my-app
```

**Now modify the application.**
**Edit app.js**
```
nano test/app.js

```
`Welcome to Cloud\n`

**Build this new image and tag it with 0.2:**
```
docker build -t node-app:0.2 .
```
**Run another container with the new image version:**
```
docker run -p 8080:80 --name my-app-2 -d node-app:0.2
docker ps
```
---------------------TASK = 4---------------------------
**you can look at the logs of a container:**
```
docker logs -f my-app-2
```
**inside the running container.**
```
docker exec -it my-app-2 bash
docker inspect my-app-2
```

---------------------TASK = 5---------------------------

Artifact Registry > Repositories.
**Click Create Repository.**

`my-repository` as the repository name.

**Region and then choose the location ** `us-central1 (Iowa)`

click > created

**Configure authentication:**
```
gcloud auth configure-docker us-central1-docker.pkg.dev
```
**Enter "Y" when prompted**
```
docker build -t us-central1-docker.pkg.dev/[project-id]/my-repository/node-app:0.2 .
docker images
docker push us-central1-docker.pkg.dev/qwiklabs-gcp-03-494d50875ec0/my-repository/node-app:0.2
```
----------------DONE-----------------------------
```
docker pull us-central1-docker.pkg.dev/[project-id]/my-repository/node-app:0.2
docker run -p 4000:80 -d us-central1-docker.pkg.dev/[project-id]/my-repository/node-app:0.2
curl http://localhost:4000
```

----------------------------------------------------

THANK YOU !
