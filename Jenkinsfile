pipeline {
    agent any

    // Declare the OWASP Dependency-Check tool to be used in this pipeline.
    // The name 'Default' must match the name configured in
    // Manage Jenkins -> Global Tool Configuration -> OWASP Dependency-Check.
    tools {
        dependencyCheck 'Default' // This refers to the tool type 'dependencyCheck' (camelCase)
                                  // and the specific installation named 'Default'.
    }

    stages {
        stage('Checkout Source Code') {
            steps {
                // Use the standard 'checkout scm' step to clone the repository
                // configured in your Jenkins job's SCM settings (e.g., branch: 'main').
                checkout scm
            }
        }

        stage('Build (Validate HTML)') {
            steps {
                echo "No complex build step required for static HTML."
                echo "Simply verifying files are present for validation/archiving."
                // Optional: You could add a linter for HTML/CSS here if you have one installed.
                // For example, if you had 'htmlhint' installed globally on the agent:
                // sh 'htmlhint index.html'
            }
        }

        stage('Security Scan - OWASP Dependency Check') {
            steps {
                echo "Running OWASP Dependency-Check..."
                // Use the 'dependencyCheck' step with 'odcInstallation' to specify
                // the globally configured tool.
                // 'additionalArguments' allows passing command-line arguments directly
                // to the Dependency-Check CLI tool.
                // For a static HTML project, this will primarily scan JavaScript files,
                // and potentially other embedded resources for known vulnerabilities.
                dependencyCheck odcInstallation: 'Default', additionalArguments: '''
                    --project HTML-Portfolio-Static
                    --format HTML
                    --out dependency-check-report
                    --scan .
                    --enableAutoUpdate
                    --failOnCVSS 7.0
                '''
            }
            post {
                always {
                    // This step publishes the Dependency-Check results to the Jenkins build page.
                    // It will look for reports in the 'dependency-check-report' directory.
                    dependencyCheckPublisher pattern: 'dependency-check-report/dependency-check-report.html'
                }
            }
        }

        stage('Archive Artifacts') {
            steps {
                // Archive general project artifacts and the Dependency-Check reports.
                // This makes the HTML/PNG/MD files and the scan reports downloadable from Jenkins.
                archiveArtifacts artifacts: '**/*.html, **/*.png, **/*.md, dependency-check-report/*', allowEmptyArchive: true
            }
        }

        stage('Deploy (optional)') {
            // This stage only runs if the current branch is 'main'.
            when {
                branch 'main'
            }
            steps {
                echo 'Deploy step placeholder. Example: rsync to a web server or GitHub Pages deploy action here.'
                // Example for AWS S3 deployment (uncomment and configure if needed):
                // sh 'aws s3 sync . s3://your-bucket-name --delete'
                // This would require AWS CLI installed on the agent and appropriate AWS credentials configured in Jenkins.
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
        // Optional: Clean the workspace after the build.
        // always {
        //     cleanWs()
        // }
    }
}
