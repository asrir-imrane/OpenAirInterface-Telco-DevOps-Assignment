### Pipeline Configuration
1. **Create a new Pipeline Job:**
   - In Jenkins, navigate to `New Item > Pipeline`.

2. **Add the Pipeline Script:**
   Example Groovy code for a simple pipeline:
   ```groovy
    pipeline {
        agent any
        stages {
            stage('Clone Repository') {
                steps {
                    git branch: 'master',
                        credentialsId: 'c2cf3e81-9069-4878-b22b-41e843d92fbc',
                        url: 'http://gitea:3000/asrir/oai-cn5g-nrf.git'
                }
            }
            stage('Build Docker Image') {
                steps {
                    sh 'docker build -t oai-nrf . -f docker/Dockerfile.nrf.ubuntu'
                }
            }
            stage('Generate Test Reports') {
                steps {
                    sh '''
                        echo "Generating test reports..."
                        mkdir -p reports
                        echo "<html><body><h1>Build Report</h1><p>Build Completed Successfully</p></body></html>" > reports/index.html
                        chmod -R 755 reports
                        ls -l reports
                    '''
                }
            }
        }
        post {
            always {
                script {
                    echo "Archiving and publishing HTML reports..."
                }
                archiveArtifacts artifacts: 'reports/**', allowEmptyArchive: false
                publishHTML([
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'reports',
                    reportFiles: 'index.html',
                    reportName: 'Build Report'
                ])
            }
            success {
                script {
                    echo "Build Successful!"
                }
            }
            failure {
                script {
                    echo "Build Failed!"
                }
            }
        }
    }

   ```
## Pipeline Overview
This section provides an overview of the pipeline setup and execution.

### Pipeline Execution
Below is the screenshot showing the pipeline stages:

![Pipeline Overview](../screenshots/pipeline_overview.png)

### Failed Pipeline Execution
Screenshot of the pipeline failure:

![Failed Pipeline](../screenshots/failed_pipeline.png)
3. **Save and Build:**
   Trigger the pipeline to test.

---
