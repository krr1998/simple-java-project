pipeline {
    agent any

    tools {
        maven 'maven'  // Use the name from Global Tool Configuration
    }

    stages {
        stage('Fetch Code') {
            steps {
                git branch: 'master', url: 'https://github.com/krr1998/simple-java-project.git'
            }
        }

        // stage('Build') {
        //     steps {
        //         sh 'mvn clean compile'
        //     }
        // }

        stage('Code Analysis') {
            environment {
                scannerHome = tool 'Sonar'
            }
            steps {
                script {
                    withSonarQubeEnv('Sonar') {
                        sh "${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=java-assingment \
                            -Dsonar.projectName=java-assingment \
                            -Dsonar.projectVersion=1.0 \
                            -Dsonar.sources=."
                    }
                }
            }
        }
    }
}
