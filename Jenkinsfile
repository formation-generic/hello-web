pipeline {
    agent any
    tools {
        maven 'M3'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/mm167/hello-web.git'
            }
        }
        stage('Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }
        stage('Test') {
            steps {
                echo "-=- Test Projet Unitaire -=-"
            }
        }
        stage('Code coverage') {
            steps {
                echo "-=- Test Code coverage -=-"
            }
        }
        stage('Sanity Check') {
            steps {
                echo "-=- Test Sanity Check -=-"
            }
        }        
        stage('Package') {
            steps {
                echo "-=- Package projet -=-"
                sh 'mvn package -DskipTests'
            }
            post {
                always {
                    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                }
            }
        } 
        stage('SSH transfert') {
            steps {
                script {
                    sshPublisher(publishers: [
                        sshPublisherDesc(configName:'docker-host', transfers:[
                            sshTransfer(
                                execCommand:'''
                                    echo "-=- Clean Up -=-";
                                    sudo docker stop simple-boot-cont || true;
                                    sudo docker rm simple-boot-cont || true;
                                    sudo docker rmi simple-boot || true;
                                '''
                            ),
                            sshTransfer(
                                sourceFiles: "target/*.jar",
                                removePrefix: "target",
                                remoteDirectory: "//home//vagrant",
                                execCommand:'''
                                    ls /home/vagrant
                                '''
                            ),
                            sshTransfer(
                                sourceFiles: "Dockerfile",
                                removePrefix: "",
                                remoteDirectory: "//home//vagrant",
                                execCommand:'''
                                    cd /home/vagrant;
                                    sudo docker build -t simple-boot .;
                                    sudo docker run -d --name simple-boot-cont -p 8080:8080 simple-boot;
                                '''
                            ),
                        ])    
                    ])
                }
            }
        }
        
        
        
        
        
        
        
        
    }
}
