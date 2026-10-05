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
        WAR_FILE = 'target/demo-0.0.1-SNAPSHOT.war'
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

        stage('Validate WAR') {
            steps {
                bat '''
                    echo ======================================
                    echo VALIDATING WAR FILE
                    echo ======================================

                    dir "%WAR_FILE%"

                    jar tf "%WAR_FILE%" > nul

                    if errorlevel 1 (
                        echo ERROR: WAR FILE IS INVALID
                        exit /b 1
                    )

                    echo WAR FILE IS VALID
                '''
            }
        }

        stage('Deploy to Local Tomcat') {
            steps {
                bat '''
                    echo ======================================
                    echo DEPLOYING TO LOCAL TOMCAT
                    echo ======================================

                    curl -u %TOMCAT_USER%:%TOMCAT_PASS% ^
                    --upload-file "%WAR_FILE%" ^
                    "%TOMCAT_URL%/deploy?path=/%APP_NAME%&update=true" ^
                    > deploy-result.txt

                    echo.
                    echo TOMCAT RESPONSE:
                    type deploy-result.txt
                    echo.

                    findstr /C:"OK" deploy-result.txt > nul

                    if errorlevel 1 (
                        echo ERROR: TOMCAT DEPLOYMENT FAILED
                        exit /b 1
                    )

                    echo TOMCAT DEPLOYMENT SUCCESSFUL
                '''
            }
        }
    }

    post {
        success {
            echo '======================================'
            echo 'CI/CD DEPLOYMENT SUCCESSFUL'
            echo '======================================'
            echo 'Application URL:'
            echo 'http://localhost:8081/webapp1'
        }

        failure {
            echo '======================================'
            echo 'CI/CD DEPLOYMENT FAILED'
            echo '======================================'
        }
    }
}
