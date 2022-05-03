pipeline {
    agent any
    tools {
        maven 'MAVEN_PATH'
        jdk 'jdk8'
    }
    stages {
        stage("Tools initialization") {
            steps {
                sh "mvn --version"
                sh "java -version"
            }
        }
        stage("Checkout Code") {
            steps {
                checkout scm
            }
        }
        stage("Check Code Health") {
            when {
                not {
                    anyOf {
                        branch 'master';
                        branch 'develop'
                    }
                }
           }
           steps {
               sh "mvn clean compile"
            }
        }
        stage("Package") {

           steps {
               sh "mvn clean package"
            }
        }
        stage("Build docker image") {
            steps {
               sh "docker buildx inspect"
               sh "docker buildx build --platform=linux/arm64  -t processor:1.0.0-arm64 ."
           }
        }
        stage("Tag & push docker image for dev") {
            when {
                branch 'develop'
            }
            steps {
               sh "docker tag processor:1.0.0-arm64 565555147445.dkr.ecr.us-west-2.amazonaws.com/processor-demo:20220429-v5-arm64"

               sh "docker push 565555147445.dkr.ecr.us-west-2.amazonaws.com/processor-demo:20220429-v5-arm64"
           }
        }
        stage("Deploy for dev") {
            when {
                branch 'develop'
            }
            steps {
               sh './deploy.sh'
            }
        }
        
        stage("Tag & push docker image for prod") {
            when {
                branch 'master'
            }
            steps {
               sh "docker tag processor:1.0.0-arm64 565555147445.dkr.ecr.us-west-2.amazonaws.com/processor-demo:20220429-v5-arm64"

               sh "docker push 565555147445.dkr.ecr.us-west-2.amazonaws.com/processor-demo:20220429-v5-arm64"
           }
        }
        
        stage("Deploy for QA") {
            when {
                branch 'master'
            }
            steps {
               sh './deploy.sh'
           }
        }
        stage("Deploy for Prod") {
            when {
                branch 'master'
            }
            steps {
               input message: 'Deploy Production Environment? (Click "Proceed" to continue)'
               sh './deploy.sh'
           }
        }
    }
 }
