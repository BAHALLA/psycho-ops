def BUILD_CURRENT_DIR
def ANSIBLE_CONFIG_PATH

pipeline {
    agent any

    stages {
        stage('Checkout') {
            post {
                failure {
                    updateGitlabCommitStatus name: 'prepare-build-environment', state: 'failed'
                }
                success {
                    updateGitlabCommitStatus name: 'prepare-build-environment', state: 'success'
                }
            }
            steps {
                checkout scm

                script {
                    BUILD_CURRENT_DIR = pwd() + "/roles"
                    echo "BUILD_CURRENT_DIR : ${BUILD_CURRENT_DIR}"
                    ANSIBLE_CONFIG_PATH = pwd() + "/ansible.cfg"
                }
            }
        }
    }


}