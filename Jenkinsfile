pipeline {
    agent any
    environment {
        REPO = 'swenum/wordpress'
    }
         stages {
                stage('Clone repository') {
                    steps {
                       script {
                                COMMIT = "${GIT_COMMIT.substring(0,8)}"
                       }
                            deleteDir()
                            git(
                                 url: 'git@github.com:Swenum/wordpress.git',
                                 credentialsId: 'Github_Repo_Swenum',
                                 branch: "master"
                             )
                    #sh 'printenv'
                    }
                }
        stage ('Docker build Nginx Php-Fpm') {
            parallel {
                stage ('Build image whit Nginx'){
                    agent { label 'docker'}
                    steps {
                        sh """
                        pwd
                        docker build -f nginx/Dockerfile -t nginx:${BUILD_NUMBER} nginx/
                        """
                    }
                    post {
                        success {
                            echo 'Tag for private registry'
                            sh "docker tag nginx:${BUILD_NUMBER} swenum/nginx:${BUILD_NUMBER}"
                        }
                    }
                }
                stage ('Build image with PHP-FPM') {
                    agent { label 'docker'}
                    steps {
                        sh "docker build -f php/Dockerfile -t php-fpm:${BUILD_NUMBER} php/"
                    }
                    post {
                        success {
                            echo 'Tag for private registry'
                            sh "docker tag php-fpm:${BUILD_NUMBER}  swenum/fpm:${BUILD_NUMBER}"
                        }
                    }
                }
                stage ('Build image with mysql') {
                     agent { label 'docker'}
                     steps {
                        sh "docker build -f mariadb/Dockerfile -t mysql:${BUILD_NUMBER} mariadb/"
                     }
                     post {
                        success {
                                   echo 'Tag for private registry'
                                   sh "docker tag mysql:${BUILD_NUMBER}  swenum/mysql:${BUILD_NUMBER}"
                        }
                     }
                 }

        }

        }
        }
    }
