
pipeline {
    agent any

    tools {
        maven 'maven3.10.0'
        jdk 'java21'
    }

    stages {

        // Stage 1: Git Checkout
        stage('Git Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/KrishnaPrasadkp1803/CI_Java_Maven.git'
            }
        }

        // Stage 2: Maven Build
        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }

        // Stage 3: Parallel Execution
        stage('Parallel Jobs') {
            parallel {

                // Job 1: Unit Testing
                stage('Unit Test') {
                    steps {
                        sh 'mvn test'
                    }
                }

                // Job 2: SonarQube Scan
                stage('SonarQube Scan') {
                    steps {
                        withSonarQubeEnv('sonarqube') {
                            sh '''
                                mvn sonar:sonar \
                                -Dsonar.projectKey=CI_Java_Maven \
                                -Dsonar.projectName=CI_Java_Maven
                            '''
                        }
                    }
                }
            }
        }

        // Stage 4: Quality Gate
        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate(abortPipeline: true)
                }
            }
        }

        // Stage 5: Nexus Artifact Upload
        stage('Nexus Upload') {
            steps {
                sh 'mvn deploy -DskipTests'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check console output.'
        }
    }
}

