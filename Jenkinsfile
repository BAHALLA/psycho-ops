def BUILD_CURRENT_DIR
def ANSIBLE_CONFIG_PATH

pipeline {
    agent any

    options {
        gitLabConnection('jenkins_gitlab_access')
        gitlabBuilds(builds: ['prepare-build-environment', 'nfs deploy'])
        buildDiscarder(logRotator(numToKeepStr: '10'))
        }

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
         stage('NFS Deploy') {
            post {
                failure {
                    updateGitlabCommitStatus name: 'nfs deploy', state: 'failed'
                }
                success {
                    updateGitlabCommitStatus name: 'nfs deploy', state: 'success'
                }
            }
            when {
                anyOf {
                    branch 'master'
                }
            }
            environment {
                ANSIBLE_ROLES_PATH = "$BUILD_CURRENT_DIR"
            }
            steps {
                gitlabBuilds(builds: ['nfs deploy']) {
                    gitlabCommitStatus("nfs deploy") {
                        script {
                            echo "ANSIBLE_ROLES_PATH : ${ANSIBLE_ROLES_PATH}"

                            wrap([$class: 'AnsiColorBuildWrapper', 'colorMapName': 'XTerm']) {
                                
                                ansiblePlaybook( 
                                    playbook: 'plays/install-nfs-playbook.yml',
                                    inventory: 'inventaires/inventaire-prod.yml', 
                                    become: true,
                                    becomeUser: "root",
                                    sudoUser: "root",
                                    colorized: true,
                                    extras: '--become-method sudo -u root -vv'
                                )
                                
                            }
                        }
                    }
                }
                
            }
        }


    }


}