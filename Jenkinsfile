
pipeline {
    agent any

    tools {nodejs "NodeJS-18-20-4"}

    environment {
        SONAR_TOKEN = credentials('global-sonar-token')
        DOJO_TOKEN = credentials('global-dojo-token')
    }

    stages {
        stage('Clone sources') {
            steps {
                git branch: 'master', credentialsId: 'github-global', url: 'https://github.com/globalhitss-devops/juice-shop.git'
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

        // stage("Build & Test"){
        //     steps{
        //         sh 'docker build -t node-app-batch-6:latest .'
        //         echo "Code Built Successfully"
        //     }
        // }

        // stage("Trivy"){
        //     steps{
        //         sh "trivy image node-app-batch-6"
        //     }
        // }

        stage("Push to Private Docker Hub Repo"){
            steps{
                withCredentials([usernamePassword(credentialsId:"DockerHubCreds",passwordVariable:"dockerPass",usernameVariable:"dockerUser")]){
                sh "docker login -u ${env.dockerUser} -p ${env.dockerPass}"
                sh "docker tag node-app-batch-6:latest ${env.dockerUser}/node-app-batch-6:latest"
                sh "docker push ${env.dockerUser}/node-app-batch-6:latest"
                }
            }
        }

        // stage("Deploy"){
        //     steps{
        //         sh "docker-compose down && docker-compose up -d"
        //         echo "App Deployed Successfully"
        //     }
        // }
    }
}