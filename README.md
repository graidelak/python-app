# Requirements

* kubectl https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/
* Docker running locally. Follow the instructions to [download and install Docker](https://docs.docker.com/desktop/)
* minikube , EKS , GKE or any kubernetes cluster
  
# Sample application

This is a simple Python application using Flask framework. The application should be a web server that returns a JSON response, when its / URL path is accessed:

{
  "timestamp": "<current date and time>",
  "hostname": "<Name of the host (IP also accepted)>",
  "engine": "<Name and/or version of the engine running>",
  "visitor ip": "<the IP address of the visitor>"
}

# How to

To test that the application is working propely in a local environment , we will use docker or minikube if you want.

First we can build the image locally:

```
docker build -t <tagname> .
```

This will generate a new image that you can use to run the application or we can just pull the image from the Dockerhub public [repository](https://hub.docker.com/r/graidelak/python-app)

```
docker pull graidelak/python-app
```

*View local images*

To list images, simply run the `docker images` command.
```
docker images
REPOSITORY                                            TAG       IMAGE ID       CREATED        SIZE
graidelak/python-app                                  latest    29791660d7c6   23 hours ago   119MB
```

## Run docker image

To run an image inside of a container, we use the `docker run` command publishing the port and in dettached mode:

```
docker run -d -p 5000:5000 graidelak/python-app
```

Now, let’s run the curl command to test the application:

```
curl localhost:5000
{"engine":"3.8.12 (default, Jan 29 2022, 05:34:25) \n[GCC 8.3.0]","hostname":"ce319bd0a844","timestamp":"Thu, 10 Feb 2022 15:51:46 GMT","visitor ip":"172.17.0.1"}
```

You can also go to your browser and type localhost:5000.


## Kubernetes Usage

Once you have your kubernetes cluster running locally with minikube or any other kubernetes cluster.

You can deploy the application to the default namespace for testing only, but it's not recommended to use that namespace to deploy applications, it's better to create a different namespace.

To run a deployment , we use the `kubectl apply` command, go to the `kubernetes-manifest` folder and run:

```
kubectl apply -f deployment.yaml -n default
kubectl apply -f service -n default
```

To test the application , we use the `kubectl port-forward` command:

```
kubectl port-forward pods/<pythonpodname> 3000:5000
```

If you use the port 5000:5000 and got this message `bind: address already in use unable to create listener: Error listen tcp6 [::1]:5000: bind: address already in use]` you must stop your docker container to free the port.

Now, let’s go to our browser and type `localhost:5000`. This will show you the response of the application.


## Remove configurations

To remove the configurations in kubernetes use:

```
kubectl delete -f deployment.yaml -n default
kubectl delete -f service.yaml -n default
```

For docker, run:
```
docker ps -a

validate the image

docker stop <containerid>
docker rm <containerid>
```