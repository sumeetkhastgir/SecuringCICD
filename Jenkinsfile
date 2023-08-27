pipeline {
    agent any

    stages {
        stage('cloning') {
            steps {
                echo "Clone project from github"
                sh 'rm -rf SecuringCICD || true'
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/sumeetkhastgir/SecuringCICD.git']])
            }
        }

        stage('Dependency Check') {
            steps {
                script {
                    // Specify path for the dependency check tool
                    def dcHome = tool name: 'DependencyCheck', type: 'DependencyCheckInstallation'
                    sh "${dcHome}/bin/dependency-check.sh --enableExperimental --scan . --format XML --out ."
                }
            }
        }

        stage('Build') {
            steps {
                echo "Current directory: ${pwd()}"
                echo "Building an application"
                sh 'docker build . -t nodejsapp'
            }
        }

        stage('Run') {
            steps {
                echo "Stopping an existing container"
                sh 'docker stop nodejsapp || true'
                sh 'docker rm nodejsapp || true'
                
                echo "Running an application"
                sh 'docker run -d -p 80:3000 --name nodejsapp nodejsapp'
            }
        }
    }

    post {
        always {
            // Record the results and archive the reports
            dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            archiveArtifacts artifacts: '**/dependency-check-report.*'
        }
    }
}
