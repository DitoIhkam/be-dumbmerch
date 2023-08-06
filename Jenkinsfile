def branch = "staging"
def repo = "git@github.com:DitoIhkam/be-dumbmerch.git"
def cred = "ssh"
def dir = "~/be-dumbmerch"
def server = "ditoihkam@103.226.139.168"
def imagename = "be-dumbmerch"
def dockerusername = "kelompok2"

pipeline {
    agent any

    stages {
        stage('Repository pull') {
            steps {
                sshagent([cred]) {
                    sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                        cd ${dir}
                        git remote add origin ${repo} || git remote set-url origin ${repo}
                        git pull origin ${branch}
                        exit
                    EOF
                    """
                }
            }
        }

        stage('Image build') {
            steps {
                sshagent([cred]) {
                    sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                        cd ${dir}
                        docker build -t ${imagename}:latest .
                        exit
                    EOF
                    """
                }
            }
        }

        stage('Running the image in a container') {
            steps {
                sshagent([cred]) {
                    sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                        cd ${dir}
                        docker container stop ${imagename} || true
                        docker container rm ${imagename} || true
                        docker run -d -p 5000:5000 --name="${imagename}"  ${imagename}:latest
                        exit
                    EOF
                    """
                }
            }
        }

        stage('Image push') {
            steps {
                sshagent([cred]) {
                    sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                        docker tag ${imagename}:latest ${dockerusername}/${imagename}:latest
                        docker image push ${dockerusername}/${imagename}:latest
                        exit
                    EOF
                    """
                }
            }
        }
    }
}
