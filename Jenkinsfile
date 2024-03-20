pipeline {
    agent any
    stages {
        stage('sonar-analysis') {
            steps {
                echo 'analyzing..'
                sh 'cd webapp && sudo docker run  --rm -e SONAR_HOST_URL="http://18.236.78.16:9000" -e SONAR_LOGIN="sqp_91d104e963591a73d97cbd93d69cb6528420a750"  -v ".:/usr/src" sonarsource/sonar-scanner-cli -Dsonar.projectKey=lms'
            }
        }
        stage('buildt') {
            steps {
                echo 'building..'
                sh 'cd webapp && npm install && npm run build'
            }
        }
        stage('nexus') {
            steps {
                script{
                    echo 'uploading dist..'
                    def packageJSON = readJSON file : 'webapp/package.json'
                    def packageJSONVersion = packageJSON.Version
                    echo "${packageJSONVersion}"
                    sh 'zip webapp/dist-${packageJSONVersion}.zip -r webapp/dist'
                    sh 'curl -v -u admin:123456 --upload-file webapp/dist-${packageJSONVersion}.zip http://18.236.78.16:8081/repository/lms/'
                }
            }
        }
        stage('deploy') {
            steps {
                script{
                    echo 'deploying..'
                    def packageJSON = readJSON file : 'webapp/package.json'
                    def packageJSONVersion = packageJSON.Version
                    echo "${packageJSONVersion}"
                    sh 'curl -u admin:123456 -X GET \'http://18.236.78.16:8081/repository/webapp/dist-${packageJSONVersion}.zip\' --output webapp/dist-${packageJSONVersion}.zip'
                    sh 'unzip webapp/dist-${packageJSONVersion}.zip'
                    sh 'sudo rm -rf /var/www/html/*'
                    sh 'sudo cp -r webapp/dist/* /var/www/html'
                }
            }
        }
    }
}
