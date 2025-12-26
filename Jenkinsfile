pipeline {
    agent any

    tools {
        maven 'Maven3'
    }

    environment {
        APP_NAME = 'springboot-demo.jar'
        APP_DIR  = '/home/jenkins/app'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building Spring Boot application...'
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application to EC2...'
                sh '''
                    set -e

                    mkdir -p $APP_DIR

                    echo "Stopping existing application (if running)..."
                    pgrep -f "$APP_NAME" | xargs kill || true

                    echo "Copying new JAR..."
                    cp target/$APP_NAME $APP_DIR/
                    chmod +x $APP_DIR/$APP_NAME

                    echo "Starting application..."
                    nohup java -jar $APP_DIR/$APP_NAME > $APP_DIR/app.log 2>&1 &

                    echo "Waiting for application to start..."
                    sleep 10

                    echo "Verifying application process..."
                    pgrep -f "$APP_NAME" || (echo "Application failed to start" && exit 1)
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully and application is running'
        }
        failure {
            echo 'Deployment failed'
        }
    }
}
