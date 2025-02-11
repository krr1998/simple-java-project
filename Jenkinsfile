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
                    // Run Lizard, print output to console & save in a file
                    def result = sh(script: 'lizard . | tee complexity_report.txt', returnStatus: true)
                    if (result != 0) {
                        error "Lizard execution failed! Check complexity_report.txt for details."
                    }
                }
                archiveArtifacts artifacts: 'complexity_report.txt', fingerprint: true
            }
        }

        stage('OWASP Dependency-Check Vulnerabilities') {
            steps {
                dependencyCheck additionalArguments: ''' 
                    -o './'
                    -s './'
                    -f 'ALL' 
                    --prettyPrint''', odcInstallation: 'OWASP'
                
                dependencyCheckPublisher pattern: 'dependency-check-report.xml'
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
