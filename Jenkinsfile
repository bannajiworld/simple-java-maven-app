pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        
        stage('Fortify Scan') {
            steps {
                sh '''
                    ${FORTIFY_HOME}/bin/sourceanalyzer -b ${PROJECT_NAME} -clean
                    ${FORTIFY_HOME}/bin/sourceanalyzer -b ${PROJECT_NAME} -Xmx4G -source 8 -cp "./target/classes" src/
                    ${FORTIFY_HOME}/bin/sourceanalyzer -b ${PROJECT_NAME} -scan -f fortify.fpr
                '''
            }
        }
	stage('Show Fortify Results') {
            steps {
                sh '''
                    ${FORTIFY_HOME}/bin/FPRUtility -listIssues -f fortify.fpr
                '''
            }
        
        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }
}
