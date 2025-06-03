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
            }
        }

        stage('Security Scan (Dependency Check)') {
            steps {
                echo "Running OWASP Dependency-Check..."
                // Use 'withTools' to ensure the 'dependency-check' executable
                // is on the PATH for the shell command within this block.
                // 'dependencyCheck' (camelCase) is the name of the tool as configured in
                // Jenkins's Global Tool Configuration -> OWASP Dependency-Check.
                withTools([dependencyCheck: 'Default']) {
                    sh 'dependency-check --scan . --format HTML --enableAutoUpdate --failOnCVSS 7.0 --project "HTML Portfolio" --out "dependency-check-report"'
                }
            }
            post {
                always {
                    dependencyCheckPublisher pattern: 'dependency-check-report/dependency-check-report.html'
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
