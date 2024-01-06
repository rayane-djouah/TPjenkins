pipeline {
    agent any
    stages {
        // Phase 1: La phase Test
        stage('Test') {
            post {
                failure {
                    script {
                        mail = "Tests failed"
                    }
                }
                success {
                    script {
                        mail = "Tests passed"
                    }
                }
            }
            steps {
                // 1. Lancement des tests unitaires.
                bat './gradlew test'
                // 2. Archivage des résultats des tests unitaires.
                junit(testResults: 'build/test-results/test/*.xml', skipPublishingChecks: true, allowEmptyResults: true)
                // 3. Génération des rapports de tests Cucumber.
                cucumber 'reports/*json'
            }
        }

        // Phase 2: La phase Code Analysis
        stage('Code Analysis') {
            post {
                failure {
                    script {
                        mail += "\nCode Analysis failed"
                    }
                }
                success {
                    script {
                        mail = "\nCode Analysis passed"
                    }
                }
            }
            steps {
                withSonarQubeEnv('sonar') {
                    bat(script: './gradlew sonarqube', returnStatus: true)
                }
                // Phase 3: La phase Code Quality
                waitForQualityGate true
            }
        }

        // Phase 4: La phase Build
        stage('Build') {
            post {
                failure {
                    script {
                        mail = "\nBuild failed"
                    }
                }
                success {
                    script {
                        mail = "\nBuild passed"
                    }
                }
            }

            steps {
                // 1. Génération du fichier Jar.
                // 2. Génération de la documentation.
                bat './gradlew build javadoc'
                // 3. Archivage du fichier Jar et de la documentation.
                archiveArtifacts 'build/libs/*.jar'
                archiveArtifacts 'build/docs/javadoc/**'
            }
        }

        // Phase 5: La phase Deploy
        stage('Deployment') {
            post {
                failure {
                    script {
                        mail = "\nDeploy failed"
                    }
                }
                success {
                    script {
                        mail = "\nDeployed successfully"
                    }
                }
            }

            steps {
                bat './gradlew publish'
                // Assuming you have a publish task for mymavenrepo.com
            }
        }

        // Phase 6: La phase Notification
        stage('Notification') {
            steps {
                mail(subject: 'Project JenkinsOGL notification', body: mail, cc: 'kr_djouah@esi.dz', bcc:'ks_oukil@esi.dz')
                slackSend(token: '', baseUrl: 'https://hooks.slack.com/services/', channel: '#general', message: 'Notification for JenkinsOGL has been sent to Slack')
            }
        }
    }
    environment {
        mail = ''
    }
}
