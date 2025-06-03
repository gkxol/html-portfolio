pipeline {
    agent any

    tools {
        // Declare the Dependency-Check tool to be used in this pipeline.
        // The name 'Default' must match the name configured in Manage Jenkins -> Global Tool Configuration.
        dependencyCheck 'Default'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/gkxol/html-portfolio.git', branch: 'main'
            }
        }

        stage('Build (Validate HTML)') {
            steps {
                echo "No build step required for static HTML."
            }
        }

        stage('Security Scan (Dependency Check)') {
            steps {
                echo "Running OWASP Dependency-Check..."
                // Removed 'tool: 'Default'' here because it's declared in the 'tools' block above.
                // The parameters 'scanPath', 'format', 'autoUpdate', 'failBuildOnCVSS'
                // are standard and should be recognized by the 'dependencyCheck' step.
                dependencyCheck scanPath: '.',
                                format: 'HTML',
                                autoUpdate: true,
                                failBuildOnCVSS: 7.0 // Example: Fail if high severity vulnerability found
            }
            post {
                always {
                    dependencyCheckPublisher()
                }
            }
        }

        stage('Code Quality Scan (SonarQube)') {
            steps {
                echo "Running SonarQube analysis..."
                withSonarQubeEnv(credentialsId: 'sonarqube-token', installationName: 'Default SonarQube Scanner') {
                    sh "sonar-scanner \
                       -Dsonar.projectKey=html-portfolio-project \
                       -Dsonar.sources=. \
                       -Dsonar.projectName='HTML Portfolio' \
                       -Dsonar.projectVersion=1.0"
                }
            }
            post {
                success {
                    echo 'SonarQube analysis successful.'
                    // timeout(time: 5, unit: 'MINUTES') {
                    //     waitForQualityGate abortPipeline: true
                    // }
                }
                failure {
                    echo 'SonarQube analysis failed or Quality Gate failed.'
                }
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: '**/*.html, **/*.png, **/*.md, dependency-check-report/*', allowEmptyArchive: true
            }
        }

        stage('Deploy (optional)') {
            when {
                branch 'main'
            }
            steps {
                echo 'Deploy step placeholder. Example: rsync or GitHub Pages deploy here.'
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
