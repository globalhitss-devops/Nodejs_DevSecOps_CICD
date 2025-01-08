
pipeline {
    agent any

    tools {nodejs "NodeJS-18-20-4"}

    environment {
        SONAR_TOKEN = credentials('global-sonar-token')
        DOJO_TOKEN = credentials('global-dojo-token')
        DOCKERHUB_CREDENTIALS = credentials('docker-hub')
    }

    stages {
        stage('Clone sources') {
            steps {
                git branch: 'master', credentialsId: 'github-global', url: 'https://github.com/globalhitss-devops/Nodejs_DevSecOps_CICD.git'
            }
        }

        // stage("SonarQube Analysis"){
        //     steps{
        //        withSonarQubeEnv("Sonar"){
        //            sh "$SONAR_HOME/bin/sonar-scanner -Dsonar.projectName=nodetodo -Dsonar.projectKey=nodetodo -X"
        //        }
        //     }
        // }

        // stage("SonarQube Quality Gates"){
        //     steps{
        //        timeout(time: 1, unit: "MINUTES"){
        //            waitForQualityGate abortPipeline: false
        //        }
        //     }
        // }

        // stage("OWASP"){
        //     steps{
        //         dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'OWASP'
        //         dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
        //     }
        // }

        stage("Docker Build"){
            steps{
                sh 'docker build -t node-app-batch-6:latest .'
            }
        }

        stage("Trivy"){
            steps{
                sh "trivy image node-app-batch-6"
            }
        }

        stage('Docker Login') {
            steps{
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }

        stage('Docker Push') {
            steps{
                sh "docker tag node-app-batch-6:latest ${env.DOCKERHUB_CREDENTIALS_USR}/node-app-batch-6:latest"
                sh "docker push ${env.DOCKERHUB_CREDENTIALS_USR}/node-app-batch-6:latest"
            }
        }

        stage("Deploy"){
            steps{
                sh "docker-compose down && docker-compose up -d"
                echo "Waiting for the container to start..."
                sleep(10)
                echo "App Deployed Successfully"
            }
        }

        stage('Test Application URL') {
            steps {
                script {
                    echo "Testing the application URL: http://localhost:8000"
                    def response = sh(script: "curl http://localhost:8000/todo --write-out %{http_code} --silent --output /dev/null", returnStdout: true).trim()

                    if (response == "200") {
                        echo "Application is up and running on http://localhost:8000"
                    } else {
                        error "Application failed to start. HTTP response code: ${response}"
                    }
                }
            }
        }
    }
    post {
         always {
             sh 'docker logout'
         }
     }
}