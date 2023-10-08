pipeline {
    agent any
    tools {
        maven 'mvn'
        jdk 'jdk:11'
    }
    environment{
        scannerHome= tool 'sonar-tool'
    }
    
    // stages {
    //     stage('Git Checkout') {
    //         steps {
                
    //             git branch: 'main', url: 'https://github.com/devprojects2023/Jfrog-Webhook-java-maven.git'
    //         }
    //     }
        
        stage('Build') {
            steps {
                // Build your Maven project
                sh 'mvn clean install'
            }
        }

        stage('sonarqube analisis') {
            steps {
                withSonarQubeEnv('Sonarurl') {
                    sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectName=Jfrog-maven -Dsonar.projectKey=Jfrog-maven -Dsonar.java.binaries=src/main/java -Dsonar.token=squ_a3b7bbd001cd7574d057ba1392886b92e760837e"
                  
                }
            }
        }
        
        stage('Publish to Jfrog Artifactory') {
            steps {
                script {
                    def server = Artifactory.server 'Jfrog01'
                    def buildInfo = Artifactory.newBuildInfo()
                    
                    buildInfo.env.capture = true
                    buildInfo.env.collect()
                    
                    def repo = 'mvn' // Replace with your repository name
                    def uploadSpec = """{
                        "files": [
                            {
                                "pattern": "target/*.jar",
                                "target": "$repo/"
                            }
                        ]
                    }"""
                    
                    // Publish the Maven artifact to the specified repository in Artifactory
                    server.upload(uploadSpec)
                    server.publishBuildInfo buildInfo
                }
            }
        }
    }
}
