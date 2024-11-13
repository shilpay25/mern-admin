pipeline {
    agent any

    environment {
        MAVEN_HOME = '/usr/share/maven'
        JAVA_HOME = '/usr/lib/jvm/java-17-openjdk-amd64'  // Ensure this matches your Java installation
        PATH = "${MAVEN_HOME}/bin:${JAVA_HOME}/bin:${env.PATH}"
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'dev',
                    credentialsId: 'shilpa25',
                    url: 'https://github.com/shilpay25/mern-admin.git'
            }
        }
        stage('Docker Build Test Container') {
            steps {
                dir('frontend')
                script {
                    // Build Docker image for testing with the current build number as a tag
                    sh 'docker build -t shilpay25/mern-admin:${BUILD_NUMBER} frontend'
                }
            }
        }

        stage('Run Tests in Docker Container') {
            steps {
                script {
                    // Run the Docker container with Maven tests
                    sh '''docker run --rm -d -p 2000:3000 \
                    -v "${WORKSPACE}:/workspace" -w /workspace/frontend \
                    shilpay25/mern-admin:${BUILD_NUMBER} sh -c "mvn -Dtest=Tests.SignUpTests clean test"'''
                }
            }
        }
        
        stage('Docker Run Production Container') {
            steps {
                script {
                    // Run the production container and map to port 3000
                    sh 'docker run --name mern-admin-prod -d -p 3000:3000 shilpay25/mern-admin:${BUILD_NUMBER}'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Investigating...'
        }
    }
}
