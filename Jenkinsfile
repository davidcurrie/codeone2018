def label = "codeone-${UUID.randomUUID().toString()}"
podTemplate(
    name: 'codeone',
    label: label,
    inheritFrom: 'default',
    containers: [
        containerTemplate(
            name: 'golang', 
            image: 'golang:1.11-alpine',
            ttyEnabled: true,
            command: 'cat'
        ),
        containerTemplate(
            name: 'kaniko', 
            image: 'dcurrie/kaniko:alpine',
            ttyEnabled: true,
            command: 'cat'
        )
    ]
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
        def repository = "registry.kube-system:80/hello"
        stage ('Docker') {
            container ('kaniko') {
                sh "executor -f `pwd`/Dockerfile -c `pwd` -d ${repository}:${commitId} --skip-tls-verify --insecure"
            }
        }
    }
}