pipeline {
    agent none
    stages {
        stage ('Test de django') { 
            agent { 
                docker { image 'python:3'
                args '-u root:root'
                }
            }
            stages {
                stage('Clonacion del repo') {
                    steps {
                        git branch:'main',url:'https://github.com/pepepfoter15/ic-imagen-python.git'
                    }
                }
                stage('Instalacion de los paquetes requeridos') {
                    steps {
                        sh 'pip install -r requirements.txt'
                    }
                }
                stage('Test del mismo') {
                    steps {
                        sh 'python3 manage.py test'
                    }
                } 
            }
        }
        stage('Subir imagen') {
            agent any
            stages {
                stage('Docker build y docker push') {
                    steps {
                        script {
                            withDockerRegistry([credentialsId: 'DOCKER_HUB', url: '']) {
                            def dockerImage = docker.build("pepepfoter15/django:${env.BUILD_ID}")
                            dockerImage.push()
                            }
                        }
                    }
                }
                stage('Eliminacion de imagen') {
                    steps {
                        script {
                            sh "docker rmi pepepfoter15/django:${env.BUILD_ID}"
                        }
                    }
                }
            }
        }
    }
    post {
        always {
            mail to: 'pepepfoter15@gmail.com',
            subject: "Status of pipeline: ${currentBuild.fullDisplayName}",
            body: "${env.BUILD_URL} has result ${currentBuild.result}"
        }
    }
}
