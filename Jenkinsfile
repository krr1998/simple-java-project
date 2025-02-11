pipeline {
    agent any

    tools {
        maven 'maven'  // Use the Maven tool configured in Jenkins
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

        stage('Cyclomatic Complexity Analysis') {
            steps {
                script {
                    // Run Lizard and generate HTML report
                    def result = sh(script: 'lizard . -H > complexity_report.html', returnStatus: true)
                    if (result != 0) {
                        error "Lizard execution failed! Check complexity_report.html for details."
                    }
                }
                // Archive the HTML report
                archiveArtifacts artifacts: 'complexity_report.html', fingerprint: true
            }
        }
    }

    post {
        success {
            echo "Build, analysis, coverage, and complexity analysis completed successfully!"
        }
        failure {
            echo "Pipeline execution failed! Check logs and reports for details."
        }
    }
}
