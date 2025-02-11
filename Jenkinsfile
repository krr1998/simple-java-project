pipeline {
    agent any

    tools {
        maven 'maven'  // Use the configured Maven installation from Jenkins Global Tool Configuration
    }

    stages {
        stage('Fetch Code') {
            steps {
                git branch: 'master', url: 'https://github.com/krr1998/simple-java-project.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean verify' // Runs tests and generates coverage reports
            }
        }

        stage('Code Analysis') {
            environment {
                scannerHome = tool 'sonar'  // SonarQube installation in Jenkins
            }
            steps {
                script {
                    withSonarQubeEnv('sonar') {
                        sh "${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=java-assingment \
                            -Dsonar.projectName=java-assingment \
                            -Dsonar.projectVersion=1.0 \
                            -Dsonar.sources=. \
                            -Dsonar.java.binaries=target/classes"
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

        stage('Cyclomatic Complexity Analysis') {
            steps {
                sh 'lizard . > complexity_report.txt' // Scans the whole project directory
                archiveArtifacts artifacts: 'complexity_report.txt', fingerprint: true
            }
        }
    }

    post {
        success {
            echo "Pipeline execution completed successfully!"
        }
        failure {
            echo "Pipeline execution failed! Check logs for details."
        }
    }
}
