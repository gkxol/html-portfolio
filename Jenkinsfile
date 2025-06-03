pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
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

        // New Stage: Security Scan using OWASP Dependency-Check
        stage('Security Scan (Dependency Check)') {
            steps {
                echo "Running OWASP Dependency-Check..."
                // The 'dependencyCheck' step is provided by the plugin.
                // tool: 'Default' refers to the name you gave in Global Tool Configuration.
                // scanPath: '.' tells it to scan the entire current workspace.
                // format: 'HTML' generates a readable HTML report.
                // autoUpdate: true ensures vulnerability definitions are updated (requires internet access).
                // failBuildOnCVSS: 7.0 (Optional) - Fails the build if a vulnerability with CVSS score 7.0 or higher is found. Adjust as needed.
                // outputDirectory: 'dependency-check-report' (Optional) - Specifies a directory for reports.
                dependencyCheck tool: 'Default',
                                scanPath: '.',
                                format: 'HTML',
                                autoUpdate: true,
                                failBuildOnCVSS: 7.0 // Example: Fail if high severity vulnerability found
            }
            post {
                always {
                    // This step publishes the Dependency-Check results to the Jenkins build page.
                    // It creates a "Dependency-Check Report" link in the build summary.
                    dependencyCheckPublisher()
                }
            }
        }

        stage('Archive Artifacts') {
            steps {
                // Archives HTML, PNG, and Markdown files as build artifacts.
                // You might also want to archive the Dependency-Check reports here.
                archiveArtifacts artifacts: '**/*.html, **/*.png, **/*.md, dependency-check-report/*', allowEmptyArchive: true
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
