pipeline {
    agent any

    stages {
        stage('sonar-analysis') {
            steps {
                echo 'analysing..'
                sh 'cd webapp && sudo docker run  --rm -e SONAR_HOST_URL="http://34.221.34.237:9000" -e SONAR_LOGIN="sqp_4fa5ceb735a3201164788acf7412dab367652732"  -v ".:/usr/src" sonarsource/sonar-scanner-cli -Dsonar.projectKey=lms'
            }    
        }
        stage('Build') {
            steps {
                echo 'Building..'
                sh 'cd webapp && npm install && npm run build'
            }
        }    
    }
}