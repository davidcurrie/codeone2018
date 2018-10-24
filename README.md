# Jenkins/Kube/Helm demo for Code One 2018

Demo project used at Oracle Code One 2018 showing deployment of Jenkins to Kubernetes with Helm and deployment from Jenkins to Kubernetes using Helm. The associated presentation can be found on [SlideShare](https://www.slideshare.net/davidcurrie/continuous-delivery-to-kubernetes-with-jenkins-and-helm-120590081).

## Pre-reqs

```bash
brew cask install minikube
minikube start
minikube addons enable registry
brew install kubectl
brew install kubernetes-helm
```

## Set up Helm

```bash
helm init --wait
```

## Deploy Jenkins

The `demo.yaml` overrides contain some settings designed to increase the speed of startup for demonstration purposes. They don't necessarily reflect what you should do in a production scenario!

```bash
helm install --name cd -f demo.yaml stable/jenkins
printf $(kubectl get secret --namespace default cd-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
minikube service cd-jenkins
```

## Deploy app

1. Create a pipeline job with a fork of this repository as the Git source for the pipeline.
2. Clicking *Build now* will push an image to the registry.
3. Run `helm create hello`.
4. Modify `service.type` in `hello/values.yaml` from `ClusterIP` to `NodePort`.
5. Replace `Jenkinsfile` with `Jenkinsfile.cd`.
6. Push changes and re-build.
