pipeline {
    agent any
    environment {
        mail = ''
    }
    stages {
        // Phase 1: La phase Test
        stage('Test') {
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
                // Additional notifications for Google Chrome and Signal as needed
            }
        }
    }
    post {
        failure {
            script {
                mail = "Build terminé avec échec"
            }
        }
        success {
            script {
                mail = "Build terminé avec succès"
            }
        }
    }
}
