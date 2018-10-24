# Jenkins/Kube/Helm demo for Code One 2018

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
4. Replace `Jenkinsfile` with `Jenkinsfile.cd`.
5. Push changes and re-build.
