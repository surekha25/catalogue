pipeline {
    // These are pre-build sections
    agent {
        node{
            label 'Agent-1'
        }
    }
    environment{
        course = "Jenkins"
        appVersion = ""
        account_id = "966656799776"
        project = "roboshop"
        component = "catalogue"
    }
    options{
        timeout(time: 10, unit: 'MINUTES')
        disableConcurrentBuilds()
    }   
    // This is Build Section
    stages {
        stage('Read Version') {
            steps {
                script{
                    def packageJSON = readJSON file: 'package.json'      
                    appVersion = packageJSON.version  
                    echo "app version: ${appVersion}"            
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                script{
                    sh """
                        npm install
                    """
                }
            }
        }
        stage('Build Image') {
            steps {
                script{
                    withAWS(region:'us-east-1',credentials:'aws-creds') {
                        sh """
                            aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${account_id}.dkr.ecr.us-east-1.amazonaws.com
                            
                            docker build -t ${project}/${component}:${appVersion} .

                            docker tag ${project}/${component}:${appVersion} ${account_id}.dkr.ecr.us-east-1.amazonaws.com/${project}/${component}:${appVersion}
                            
                            docker images
                            
                            docker push ${account_id}.dkr.ecr.us-east-1.amazonaws.com/${project}/${component}:${appVersion}
                        """
                    }
                                        
                }
            }
        }
    }
    post{
        always{
            echo "I will always say Hello again!"
            cleanWs()
        }
        success{
            echo "I will run if success"
        }
        failure{
            echo "I will run if failure"
        }
        aborted{
            echo "Pipeline is aborted"
        }
    }
}