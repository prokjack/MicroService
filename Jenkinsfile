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
                imageTag = readFile('.git/commit-id').trim()
            }
            stage('Docker Build, Push'){
                withDockerRegistry([credentialsId: "${creds}", url: 'http://10.0.0.26:5000']) {
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