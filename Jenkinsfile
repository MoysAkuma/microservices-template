pipeline {
    //agent any
    agent { 
        docker { 
            image 'maven:3.3.3' 
        } 
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Packaging') {
            steps {
                sh 'mvn install'
            }
        }
        stage('Deploy - Staging') {
            steps {
                sh './deploy.sh staging'
            }
        }
        stage('Deploy - Production') {
            steps {
                sh './deploy.sh production'
            }
        }
    }
    post {
        always {
            echo 'Build finished.'
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            junit 'target/surefire-reports/*.xml'
            deleteDir()
        }
        success {
            echo 'The build succeeded'
            
            // Comment/Uncomment the ones you would need/use

            // Send email
            mail to: 'colin.but@email.com',
                 subject: "Passed Pipeline: ${currentBuild.fullDisplayName}",
                 body: "${env.BUILD_URL} - ${env.JOB_NAME} #${env.BUILD_NUMBER} has passed."

            // Atlassian Hipchat Integration
            hipchatSend message: "@here ${currentBuild.fullDisplayName} has successfully completed.", 
                        color: 'GREEN'

            // Slack Integration
            slackSend channel: '#room',
                      color: 'good',
                      message: "The pipeline ${currentBuild.fullDisplayName} has successfully completed."
        }
        failure {
            echo 'The build failed'
            
            // Comment/Uncomment the ones you would need/use

            // Send email
            mail to: 'colin.but@email.com',
                 subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
                 body: "Something went wrong with ${env.BUILD_URL}"

            // Atlassian Hipchat Integration
            hipchatSend message: "@here ${env.JOB_NAME} #${env.BUILD_NUMBER} has failed.", 
                        color: 'RED'

            // Slack Integration
            slackSend channel: '#room',
                      color: 'bad',
                      message: "${env.JOB_NAME} #${env.BUILD_NUMBER} has failed."
        }
        unstable {
            echo 'The build was marked unstable'
        }
        changed {
            echo 'The state of the pipeline has changed'
        }
    }
}