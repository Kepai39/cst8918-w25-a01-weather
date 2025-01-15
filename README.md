CST8918 - DevOps: Infrastructure as Code  
Prof: Robert McKenney
Student: Catherine Daigle-41175118

# LAB-A01 Weather App

In this hands-on lab activity we will lay the foundations for adopting infrastructure as code (IaC) using a simple Node.js based web application and Kubernetes on Docker Desktop.

## Objectives

- Review general application requirements and solution implementation choices
- Review the benefits of containerization
- Get an introduction to Kubernetes

## Part 1 - Web Application

### Scenario

use the [OpenWeather API](https://openweathermap.org) as the source of weather data. Calls to this API require an API Key (access token) which should remain private. To avoid exposing this API Key to the browser, the solution will require a backend API server to proxy the calls to OpenWeather.

```sh
# linux/mac
export WEATHER_API_KEY=<your-api-key>
```

### Test the application

- run `npm install` to pull-in the various libraries and dev dependencies (e.g. remix, react, etc)
- start the dev server with `npm run dev`
- open link displayed in your terminal with a browser, e.g. http://localhost:59287

## Part 2 - Kubernetes Deployment

### Create a container image
#### Dockerfile
Dockerfile included at the top level of the project files.

#### Build the container image

In the terminal, 
`docker build --tag=cst8918-a01-weather-app .`

#### Test the container
```sh
docker run -d --name weather -p 8080:8080 --env WEATHER_API_KEY=<your-api-key> cst8918-a01-weather-app
```

Then open `http://localhost:8080` 

#### Push the container image to the registry

```sh
docker login docker.io
```

```sh
docker tag cst8918-a01-weather-app <docker-hub-username>/cst8918-a01-weather-app
docker push <docker-hub-username>/cst8918-a01-weather-app
```

### Working with Kubernetes Locally
```sh
kubectl config use-context docker-desktop
```

#### Namespace

Activate that namespace with the 
`kubectl apply -f ./k8s/a01_namespace.yaml` 
command in the terminal.

#### Deployment

##### API Key secret
Run `kubectl create secret generic weather --from-literal='api-key=<your-secret-api-key>' -n cst8918` 

Activate the deployment with `kubectl apply -f ./k8s/a01_deployment.yaml -n cst8918`.

#### Service
Activate the service with `kubectl apply -f ./k8s/a01_service.yaml -n cst8918`. 

Now test by opening `http:localhost` in a browser.

#### Clean-up

When you are all done, shut down all of the resources that are no longer needed.

```sh
#!/bin/sh
kubectl delete deployment weather-app-deployment -n cst8918
kubectl delete service weather-app-service -n cst8918
kubectl delete secret weather -n cst8918
kubectl delete namespace cst8918
```

## Demo
Screenshot:

- Browser with the app running at `http://localhost`
- `kubectl get namespaces`
- `kubectl get services -n cst8918`
- `kubectl get pods -n cst8918`

The Screenshot hand-in:
![The Screenshot hand in](<Screenshot 2025-01-15 at 2.48.18 PM.png>)

Files:

- all files in the `k8s` folder
