pipeline {
    agent any

    tools {
        // CORRECTED: Changed 'dependency-check' to 'dependencyCheck' (camelCase).
        // This is the common convention for tool names in the 'tools' block of Declarative Pipelines.
        dependencyCheck 'Default' // 'Default' must match the name configured in Global Tool Configuration
        // Also ensure your SonarQube Scanner is defined here if you configured it as a tool
        // sonarqubeScanner 'Default SonarQube Scanner' // Uncomment if you configured SonarQube Scanner as a tool
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
                // Execute the dependency-check CLI tool directly via shell command.
                // The 'tools' block above ensures 'dependency-check' executable is in PATH.
                sh 'dependency-check --scan . --format HTML --enableAutoUpdate --failOnCVSS 7.0 --project "HTML Portfolio" --out "dependency-check-report"'
            }
            post {
                always {
                    // Publish the Dependency-Check report from the specified output directory.
                    dependencyCheckPublisher pattern: 'dependency-check-report/dependency-check-report.html'
                }
            }
        }

        stage('Code Quality Scan (SonarQube)') {
            steps {
                echo "Running SonarQube analysis..."
                // Use withSonarQubeEnv to set up the SonarQube environment.
                // 'credentialsId' and 'installationName' must match your Jenkins configuration.
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
                    // Uncomment the following block to enforce a Quality Gate.
                    // timeout(time: 5, unit: 'MINUTES') { // Give it a timeout to wait for analysis
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
                // Archive all relevant artifacts, including the Dependency-Check reports.
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
