pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                // Explicitly specify the 'main' branch for the git clone operation.
                // Without this, Jenkins's 'git' step often defaults to 'master',
                // which was causing the "Couldn't find any revision to build" error.
                git url: 'https://github.com/gkxol/html-portfolio.git', branch: 'main'
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
                // Archives HTML, PNG, and Markdown files as build artifacts.
                // allowEmptyArchive: true ensures the step doesn't fail if no matching files are found.
                archiveArtifacts artifacts: '**/*.html, **/*.png, **/*.md', allowEmptyArchive: true
            }
        }

        stage('Deploy (optional)') {
            // This stage only runs if the current branch is 'main'.
            when {
                branch 'main'
            }
            steps {
                echo 'Deploy step placeholder. Example: rsync or GitHub Pages deploy here.'
                // Example for AWS S3 deployment (uncomment and configure if needed):
                // sh 'aws s3 sync . s3://your-bucket-name --delete'
            }
        }
    }

    // Post-build actions based on the build result.
    post {
        success {
            echo 'Site build successful!'
        }
        failure {
            echo 'Build failed.'
        }
    }
}
