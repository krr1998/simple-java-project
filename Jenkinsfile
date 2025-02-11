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

        stage('Build') {
            steps {
                sh 'mvn clean verify'
            }
        }

        stage('Code Analysis') {
            environment {
                scannerHome = tool 'sonar'
            }
            steps {
                script {
                    withSonarQubeEnv('sonar') {
                        sh "${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=java-assingment \
                            -Dsonar.projectName=java-assingment \
                            -Dsonar.projectVersion=1.0 \
                            -Dsonar.sources=. \
                            -Dsonar.java.binaries=."
                    }
                }
            }
        }

        stage('Code Coverage') {
            steps {
                script {
                    jacoco(
                        execPattern: '**/target/jacoco.exec',
                        classPattern: '**/target/classes',
                        sourcePattern: '**/src/main/java',
                        inclusionPattern: '**/*.class',
                        exclusionPattern: '**/test/**/*.class'
                    )
                }
            }
        }
    }

    post {
        success {
            echo "Build and analysis completed successfully!"
        }
        failure {
            echo "Build failed! Check logs for details."
        }
    }
}
