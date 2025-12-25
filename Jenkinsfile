pipeline { 
    agent any

    tools {
        maven 'Maven3'
    }

    environment {
        APP_NAME = "springboot-demo.jar"
        APP_DIR  = "/home/jenkins/app"  // Updated path, owned by Jenkins
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
                    mkdir -p $APP_DIR
                    echo "Stopping existing application (if any)..."
                    pkill -f $APP_NAME || true

                    echo "Copying new JAR..."
                    cp target/*.jar $APP_DIR/$APP_NAME
                    chmod +x $APP_DIR/$APP_NAME

                    echo "Starting application..."
                    nohup java -jar $APP_DIR/$APP_NAME > $APP_DIR/app.log 2>&1 &
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully'
        }
        failure {
            echo 'Deployment failed'
        }
    }
}
