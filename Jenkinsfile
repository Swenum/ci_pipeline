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
                    sh 'printenv'
                    }
                }
        stage ('Docker build Nginx Php-Fpm') {
            parallel {
                stage ('Build image whit Nginx'){
                    agent { label 'docker'}
                    steps {
                        sh """
                        pwd
                        docker build -f nginx/Dockerfile -t nginx nginx/
                        """
                    }
                    post {
                        success {
                            echo 'Tag for private registry'
                            sh "docker tag  swenum/nginx"
                        }
                    }
                }
                stage ('Build image with PHP-FPM') {
                    agent { label 'docker'}
                    steps {
                        sh "docker build -f php7-fpm/Dockerfile -t fpm php/"
                    }
                    post {
                        success {
                            echo 'Tag for private registry'
                            sh "docker tag swenum/fpm:$BUILD"
                        }
                    }
                }
                stage ('Build image with mysql') {
                     agent { label 'docker'}
                     steps {
                        sh "docker build -f mysql/Dockerfile -t mysql:$BUILD mysql/"
                     }
                     post {
                        success {
                                   echo 'Tag for private registry'
                                   sh "docker tag swenum/mysql:$BUILD"
                        }
                     }
                 }

        }

        }
        }
    }
