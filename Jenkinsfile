pipeline {
    agent any
    environment {
        BUILDVAR = "var_build_number"
        TOKEN = credentials("KubernetesToken")
        K8S_URL = credentials("URL")
    }
    stages {
        stage('Clone Repo') {
            steps {
                git url: 'https://github.com/andrro2/enterprise_battleship_react', branch:'dev'
            }
        }
        stage('Create Container') {
            steps {
                sh "docker build -t andrro/enterprise_battleship_react:latest ."
            }
        }
    }
    post {
        success {
            withCredentials([usernamePassword(credentialsId: 'docker-credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                sh "docker login -u ${USERNAME} -p ${PASSWORD}"
                sh "docker push andrro/enterprise_battleship_react:latest"
                sh "sed -i -e 's#${BUILDVAR}#${BUILD_NUMBER}#' deployment.json"
                sh "curl https://${K8S_URL}:6443/apis/apps/v1/namespaces/default/deployments/battleship-react  \
                -k -H 'Content-Type: application/json' -H 'Authorization: Bearer ${TOKEN}' --data @deployment.json --request PUT"
            }
        }
    }
}
