#!groovy
node {
    def git
    def commitHash
    def buildImage

    stage('Checkout') {
        git = checkout scm
        commitHash = git.GIT_COMMIT
    }

    stage('Test') {
        try{
            sh './gradlew check'
        finally {
            junit 'build/reports/**/*.xml'
        }
    }

    stage('Build') {
        sh './gradlew build -x test'

    }

    stage('Build Docker Image') {
        buildImage = docker.build("hubtea/spring-cloud-config:${commitHash}")
    }

    stage('Archive') {
        parallel (
            "Archive Artifacts" : {
                archiveArtifacts artifacts: '**/build/libs/*.jar', fingerprint: true
            },

            "Docker Image Push" : {
                docker.withRegistry('https://registry.hub.docker.com', 'docker-hub') {
                    buildImage.push("latest")
                }
            }
        )
    }
}
