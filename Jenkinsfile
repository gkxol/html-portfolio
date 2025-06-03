pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/gkxol/html-portfolio.git'
            }
        }

        stage('Build (Validate HTML)') {
            steps {
                echo "No build step required for static HTML."
                // Optional: run a validator if needed
                // sh 'htmlhint index.html'
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: '**/*.html, **/*.png, **/*.md', allowEmptyArchive: true
            }
        }

        stage('Deploy (optional)') {
            when {
                branch 'main'
            }
            steps {
                echo 'Deploy step placeholder. Example: rsync or GitHub Pages deploy here.'
                // Example for AWS S3:
                // sh 'aws s3 sync . s3://your-bucket-name --delete'
            }
        }
    }

    post {
        success {
            echo 'Site build successful!'
        }
        failure {
            echo 'Build failed.'
        }
    }
}
