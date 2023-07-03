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
                        sh "rm -rf gcp*"
                        sh "git clone ${params.REPO_URL}"
                    }
                }
            }
        }

        stage('Run Playbook') {
            steps {
                dir('ansible') {
                    script {
                        // Assuming your playbook file is named 'playbook.yml'
                        sh "ansible-playbook playbook.yml"
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
