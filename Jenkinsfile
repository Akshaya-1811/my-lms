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
        stage('nexus-release') {
            steps {
                script {
                    echo "Publish LMS Artifacts"       
                    def packageJSON = readJSON file: 'webapp/package.json'
                    def packageJSONVersion = packageJSON.version
                    echo "${packageJSONVersion}"  
                    sh "zip webapp/dist-${packageJSONVersion}.zip -r webapp/dist"
                    sh "curl -v -u admin:123456 --upload-file webapp/dist-${packageJSONVersion}.zip http://34.221.34.237:8081/repository/lms/"     
                }
            }
        } 
        stage('deploy') {
            steps {
                script {
                    echo "Deploy LMS"       
                    def packageJSON = readJSON file: 'webapp/package.json'
                    def packageJSONVersion = packageJSON.version
                    echo "${packageJSONVersion}"  
                    sh "curl -u admin:123456 -X GET \'http://34.221.34.237:8081/repository/lms/dist-'${packageJSONVersion}'.zip' --output dist-'${packageJSONVersion}'.zip"
                    sh 'sudo rm -rf /var/www/html/*'
                    sh "sudo unzip -o dist-'${packageJSONVersion}'.zip"
                    sh "sudo cp -r webapp/dist/* /var/www/html"
                }
            }
        }      
    }
}
