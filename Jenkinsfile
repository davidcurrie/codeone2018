def label = "codeone-${UUID.randomUUID().toString()}"
podTemplate(name: 'codeone', label: label, yaml: """
kind: Pod
metadata:
  name: codeone
spec:
  containers:
  - name: golang
    image: golang:1.11-alpine
    imagePullPolicy: Always
    command:
    - cat
    tty: true
  - name: kaniko
    image: dcurrie/kaniko:alpine
    imagePullPolicy: Always
    command:
    - cat
    tty: true
    volumeMounts:
      - name: jenkins-docker-cfg
        mountPath: /root
  volumes:
  - name: jenkins-docker-cfg
    secret:
      secretName: regcred
      items:
      - key: .dockerconfigjson
        path: .docker/config.json
"""
) {
    node(label) {
        def commitId
        stage ('Extract') {
            commitId = checkout(scm).GIT_COMMIT.take(8)
        }
        stage ('Build') {
            container ('golang') {
                sh 'CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o main .'
            }
        }
        def repository
        stage ('Docker') {
            container ('kaniko') {
                repository = "index.docker.io/dcurrie/hello"
                sh "executor -f `pwd`/Dockerfile -c `pwd` -d ${repository}:${commitId} --skip-tls-verify"
            }
        }
    }
}
