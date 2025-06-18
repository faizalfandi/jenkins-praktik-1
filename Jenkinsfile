pipeline {
    agent {
        docker {
            image 'python:3.10' 
        }
    }

    environment {
        VENV = 'view'
    }
    
    stages {
        stage('Setup Environment & Install Dependencies') {
            steps {
                sh '''
                    python -m venv $VENV           
                    . $VENV/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt 
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                    . $VENV/bin/activate     i
                    pytest test_app.py 
                 '''
            }
        }

        stage('Deploy') {
            when {
                // Tahap Deploy hanya akan berjalan jika branch adalah 'main'
                // atau branch yang cocok dengan pola regex 'release/.*'
                anyOf {
                    branch 'main'
                    branch pattern: 'release/.*', comparator: "REGEXP"
                }
            }
            steps {
                echo "Simulating deploy from branch ${env.BRANCH_NAME}"
                // Di sini Anda akan menambahkan langkah-langkah deployment yang sebenarnya,
                // seperti menyalin file ke server, membangun Docker image, dll.
            }
        }
    }

    post {
        // Tindakan setelah pipeline selesai, baik sukses maupun gagal
        success {
            script {
                def payload = [
                    content: "✅ Build Success on `${env.BRANCH_NAME}`\nURL: ${env.BUILD_URL}"
                ]
                httpRequest(
                    httpMode: 'POST',
                    contentType: 'APPLICATION_JSON',
                    requestBody: groovy.json.JsonOutput.toJson(payload),
                    url: 'https://discord.com/api/webhooks/1384903814041178144/xfMWMzjv8y9p8bAmW48btnez_or9hxbAeYi821Buctfc7ltzwdk90kJTqpAnNnaJafbs'
                )
            }
        }
        failure {
            script {
                def payload = [
                    content: "❌ Build FAILED on `${env.BRANCH_NAME}`\nURL: ${env.BUILD_URL}"
                ]
                httpRequest(
                    httpMode: 'POST',
                    contentType: 'APPLICATION_JSON',
                    requestBody: groovy.json.JsonOutput.toJson(payload),
                    url: 'https://discord.com/api/webhooks/1384903814041178144/xfMWMzjv8y9p8bAmW48btnez_or9hxbAeYi821Buctfc7ltzwdk90kJTqpAnNnaJafbs'
                )
            }
        }
    }
}
