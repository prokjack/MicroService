podTemplate(label: label, containers: [
  containerTemplate(name: 'gradle', image: 'gradle:4.5.1-jdk9', command: 'cat', ttyEnabled: true),
  containerTemplate(name: 'docker', image: 'docker', command: 'cat', ttyEnabled: true),
  containerTemplate(name: 'kubectl', image: 'lachlanevenson/k8s-kubectl:latest', command: 'cat', ttyEnabled: true),
  containerTemplate(name: 'helm', image: 'lachlanevenson/k8s-helm:latest', command: 'cat', ttyEnabled: true)
],
volumes: [
  hostPathVolume(mountPath: '/home/gradle/.gradle', hostPath: '/tmp/jenkins/.gradle'),
  hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock')
])

node{
    def Namespace = "development"
    def project = "my-project"
    def appName = "microservice"
    def imageVersion = "development"
    def imageTag = "localhost:5000/${project}:${appName}:${imageVersion}:${env.BUILD_NUMBER}"
    def ImageName = "microservice"
    def creds = "docker-cred-id"

    try{
            stage('Checkout'){
                git 'https://github.com/prokjack/MicroService.git'
                sh "git rev-parse --short HEAD > .git/commit-id"
                imageTag= readFile('.git/commit-id').trim()
            }
            stage('Docker Build, Push'){
                withDockerRegistry([credentialsId: "${creds}", url: 'http://localhost:5000']) {
                sh """
                    docker build -t ${imageTag} .
                    docker push ${ImageName}
                    """
                }
            }
            stage('Deploy on K8s'){
                sh "ansible-playbook /var/lib/jenkins/ansible/sayarapp-deploy/deploy.yml  --user=jenkins --extra-vars ImageName=${ImageName} --extra-vars imageTag=${imageTag} --extra-vars Namespace=${Namespace}"
            }
    } catch (err) {
        print err
        echo 'Something failed, I should sound the klaxons!'
        throw err
    }
}