node{
    def Namespace = "development"
    def project = "my-project"
    def appName = "microservice"
    def imageVersion = "development"
    def imageTag = "localhost:5000/${project}/${appName}:${imageVersion}.${env.BUILD_NUMBER}"
    def ImageName = "microservice"
    def Creds = "2dfd9d0d-a300-49ee-aaaf-0a3efcaa5279"

    try{
        stage('Checkout'){
            git 'https://github.com/prokjack/MicroService.git'
                  sh "git rev-parse --short HEAD > .git/commit-id"
                  imageTag= readFile('.git/commit-id').trim()
        }
        stage('Docker Build, Push'){
            withDockerRegistry([credentialsId: "${Creds}", url: 'http://localhost:5000/v2/']) {
                sh "docker build -t ${ImageName}:${imageTag} ."
                sh "docker push ${ImageName}"
            }
        }
        stage('Deploy on K8s'){
            sh "ansible-playbook /var/lib/jenkins/ansible/sayarapp-deploy/deploy.yml  --user=jenkins --extra-vars ImageName=${ImageName} --extra-vars imageTag=${imageTag} --extra-vars Namespace=${Namespace}"
        }
    } catch (err) {
        currentBuild.result = 'FAILURE'
    }
}