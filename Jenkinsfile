pipeline {
    agent any

    parameters {
        string(name: 'REPO_URL', description: 'Ansible Playbook Repository URL')
    }

    stages {
        stage('Clone Repository') {
            steps {
                dir('ansible') {
                    script {
                        sh "git clone ${params.REPO_URL}"
                    }
                }
            }
        }
    }

    post {
        always {
            deleteDir()
        }
    }
}
