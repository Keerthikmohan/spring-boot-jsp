pipeline {
    agent any

    tools {
        maven '3.8.5'
    }

    environment {
        APP_VERSION = sh(
                    script: '''
                            perl -nle 'print "$1" if /<version>(v\\d+\\.\\d+\\.\\d+)<\\/version>/' pom.xml
                        ''',
                        returnStdout: true
                    ).trim()
    }

    stages {
        stage('Source') {
            steps {
                git branch: 'improved', changelog: false, credentialsId: '0438ba22-1885-4389-b8e8-d1fddd91da56', poll: false, url: 'https://github.com/Keerthikmohan/spring-boot-jsp.git'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn package'
            }
        }
        stage('Publishing Artifcats') {
            steps {
                withAWS(credentials: 'buckets3', region: 'us-east-1') {
    
                    s3Upload(file:"target/news-${APP_VERSION.trim()}.jar", bucket:'keerthinibin', path:'keerthinibin/spring_boot/')
                }
            }
        }
        stage('Deploying Artifcats') {
            steps {
                sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@54.210.74.72 "sudo ~/deploy.sh ${APP_VERSION}"
                '''
            }
        }
    }
}
