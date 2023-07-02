pipeline {
    agent any

    parameters {
        string(name: 'REPO_URL', description: 'Ansible Playbook Repository URL')
        choice(name: 'LIFECYCLE', choices: ['dev', 'staging','cert-ncde','cert-cde', 'prod-cde'], description: 'Select the Lifecycle')
        string(name: 'INVENTORY_FILE', description: 'Inventory file path', defaultValue: "inventories/${params.LIFECYCLE}.gcp.yml")
        string(name: 'PLAYBOOK_FILE', description: 'Playbook file path', defaultValue: "playbooks/${params.LIFECYCLE}/playbook.yml")
    }

    options {
        timestamps()
    }

    environment {
        ANSIBLE_FORCE_COLOR = 'true'
        GOOGLE_APPLICATION_CREDENTIALS = credentials('gcp-ansible-key')
        SSH_KEY_NAME = "${WORKSPACE}/gpec-svcs-ansible-ssh-key"
    }

    stages {
        stage('Initialisation') {
            steps {
                dir('ansible') {
                    script {
                        withCredentials([file(credentialsId: 'gpec-svcs-ansible-ssh-key', variable: 'SSH_KEY')]) {
                            sh "mv ${SSH_KEY} ${SSH_KEY_NAME}"
                            sh "chmod 600 ${SSH_KEY_NAME}"
                        }
                    }
                }
            }
        }

        stage('Clone Repository') {
            steps {
                dir('ansible') {
                    script {
                        sh "git clone ${REPO_URL}"
                        script {
                            def repoName = getRepoName(REPO_URL)
                            env.REPO_NAME = repoName
                        }
                        echo "The Playbook that will be Installed is : ${env.REPO_NAME}"
                    }
                }
            }
        }

        stage('Install Ansible Dependencies') {
            steps {
                dir("ansible/${env.REPO_NAME}") {
                    sh 'ansible-galaxy install -r requirements.yml --force -v -c'
                }
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                dir("ansible/${env.REPO_NAME}") {
                    withEnv(["SSH_KEY=${SSH_KEY_NAME}" ]) {
                    sh '''
                    ansible-playbook -i dev.gcp.yml playbooks/dev/playbook.yml -C
                    '''
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

def getRepoName(repoUrl) {
    // Extract the folder name from the repoUrl
    def folderName = repoUrl.substring(repoUrl.lastIndexOf('/') + 1, repoUrl.lastIndexOf('.git'))

    return folderName
}
