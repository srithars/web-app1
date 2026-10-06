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
        WAR_FILE = 'target/demo-0.0.1-SNAPSHOT.war'
    }

    stages {

        stage('Clone') {
            steps {
                git url: 'https://github.com/srithars/web-app1.git',
                    branch: 'main'
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('Deploy') {
            steps {
                bat '''
                    curl -u %TOMCAT_USER%:%TOMCAT_PASS% ^
                    --upload-file "%WAR_FILE%" ^
                    "%TOMCAT_URL%/deploy?path=/webapp1&update=true"
                '''
            }
        }
    }
}
