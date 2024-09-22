pipeline {
    agent any
    tools{
        maven 'Maven_def'
    }
    environment {
        SONAR_HOST_URL = 'http://35.197.67.57:9000'  // SonarQube server URL
    }
   
    stages {
        stage("clone repo"){
            steps{
            git branch: 'master', url: 'https://github.com/chereddynag/mvn_sonar.git'
            }
        }

        // stage('Build') {
        //     steps {
        //         // Run the Maven build (or another build tool you're using)
        //         sh '/opt/maven/bin/mvn clean install'
        //     }
        // }

        stage('SonarQube Analysis') {
            steps {
                // This is the correct way to set up SonarQube analysis
                withSonarQubeEnv('sonarqube') {  // Replace 'SonarQube' with the actual name of your SonarQube server in Jenkins config
                    withCredentials([usernamePassword(credentialsId: 'sonar_ucreds', usernameVariable: 'SONAR_USER', passwordVariable: 'SONAR_PASS')]) {
                        sh """
                            /opt/maven/bin/mvn sonar:sonar \
                            -Dsonar.projectKey=my-project \
                            -Dsonar.host.url=${SONAR_HOST_URL} \
                            -Dsonar.login=${SONAR_USER} \
                            -Dsonar.password=${SONAR_PASS}
                        """
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                // Wait for the quality gate result and fail the build if it fails
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline execution completed."
        }
    }
}