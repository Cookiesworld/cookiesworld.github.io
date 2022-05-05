---
layout: post
title: "Using secrets to update docker build environment variables"
date: 2022-05-01 11:07:55 +0000
categories: personal
toc: false
---
I updated my [wordle helper](https://wh.azurewebsites.net) to add in the ability to select known letters at position elements. Using indexOf to test if the letter is contained at that position.

I also added [application insights](https://docs.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview) which will allow me to track errors via the application error boundary checking. It also give insights into sessions and users. On first use on the deployed version the event tracking failed. What I soon realised is that though locally I had the application insights key the deployed version did not.

To deploy I am using github actions which automatically run the unit test, build a docker image and push that image to an azure application. I needed a way to inject this into the build process. It was (for me) quite complicated to get the application insights key into the build chain without getting it in the codebase. For the key I used an environement variable called 
``` 
REACT_APP_APPINSIGHTS_INSTRUMENTATIONKEY=3fc8334d-b4b0-4e5d-820b-b4af657aff34
```
NB this key is for reference only not a real one. Locally this works as when the application is built the environment variable can be read using process.env.REACT_APP_APPINSIGHTS_INSTRUMENTATIONKEY. NB in react apps all environment variables must be prefixed with REACT_APP.

Firstly in github I created a secret called REACT_APP_APPINSIGHTS_INSTRUMENTATIONKEY. I then modified the [github action file](https://github.com/Cookiesworld/wordle-solver/blob/master/.github/workflows/master_wh.yml) adding an env section which pulled in the secret, then added a build argument tag to the build and push container job.

```
 - name: Build and push container image to registry
      uses: docker/build-push-action@v2
      with:
        push: true
        tags: cookie.azurecr.io/${{ secrets.AzureAppService_ContainerUsername_2de0e5fe1f1543c28eaf7ffea7447ea7 }}/wordle-node:${{ github.sha }}
        file: ./Dockerfile
        build-args: API_KEY=${{ secrets.REACT_APP_APPINSIGHTS_INSTRUMENTATIONKEY }}
```

Finally on the [Dockerfile](https://github.com/Cookiesworld/wordle-solver/blob/Cookiesworld-patch-1/Dockerfile) I added a docker argument which immediately set the environment variable for REACT_APP_APPINSIGHTS_INSTRUMENTATIONKEY to the key passed in. This now meant that when the npm install --production ran it should pick up the application insights key I passed in.

Once I pushed the changes the application rebuilt and the track events started being fired using the correct key.
