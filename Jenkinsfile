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

        stage('Deploy to Local Tomcat') {
            steps {
                bat '''
                    for %%F in (target\\*.war) do (
                        echo Deploying %%F
                        curl -u %TOMCAT_USER%:%TOMCAT_PASS% ^
                        --upload-file "%%F" ^
                        "%TOMCAT_URL%/deploy?path=/%APP_NAME%&update=true" ^
                        > deploy-result.txt
                    )

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
