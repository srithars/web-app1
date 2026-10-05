pipeline {
    agent any

    tools {
        jdk 'JDK_HOME'
        maven 'MAVEN_HOME'
    }

    environment {
        TOMCAT_URL = 'http://localhost:8081/manager/text'
        TOMCAT_USER = 'admin'
        TOMCAT_PASS = 'admin'

        APP_NAME = 'webapp1'
        WAR_NAME = 'webapp1.war'
    }

    stages {

        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/srithars/web-app1.git',
                    branch: 'main'
            }
        }

        stage('Build Application') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('Prepare WAR') {
            steps {
                bat 'copy /Y target\\*.war %WAR_NAME%'
            }
        }

        stage('Deploy to Local Tomcat') {
            steps {
                bat '''
                    curl -u %TOMCAT_USER%:%TOMCAT_PASS% ^
                    --upload-file %WAR_NAME% ^
                    "%TOMCAT_URL%/deploy?path=/%APP_NAME%&update=true" ^
                    > deploy-result.txt

                    type deploy-result.txt

                    findstr /C:"OK" deploy-result.txt

                    if %ERRORLEVEL% NEQ 0 (
                        echo Deployment failed!
                        exit /b 1
                    )
                '''
            }
        }
    }

    post {
        success {
            echo '======================================'
            echo 'DEPLOYMENT SUCCESSFUL'
            echo '======================================'
            echo 'Application URL:'
            echo 'http://localhost:8081/webapp1'
        }

        failure {
            echo '======================================'
            echo 'BUILD OR DEPLOYMENT FAILED'
            echo '======================================'
        }
    }
}
