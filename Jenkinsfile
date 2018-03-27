pipeline {
  agent any
  stages {
    stage('test') {
      steps {
        sh '''#!groovy
node { 
    def git
    def commitHash
    def buildImage
    
    stage (\'Checkout\'){
        git = checkout scm
        commiHash = git.GIT_COMMIT
    }
    
    stage(\'Test\'){
        try{
            sh \'./gradlew check\'
        } finally {
            junit \'build/test-results/**/*.xml\'
        }
    }
    
    stage(\'Build\'){
        sh \'./gradlew build -x test\'
    }
    
    stage(\'Build Docker Image\'){
        buildImage = docker.build("namejewoo/spring-cloud-hystrix:${commitHash}")
    }
    
    stage(\'Archive\'){
        parallel(
            "Archive Artifacts" :{
                archiveArtifacts artifacts: \'**build/libs/*.jar\', fingerprint: true
                },
                "Docker Image Push" :{
                    buildImage.push("${commitHash}")
                    buildImage.push("latest")
                }
            )
    }
    
    stage(\'kubernates Deploy\') {
        sh \'kubectl apply --namespace=deveopment -f deployment.yml\'
    }
    }

'''
      }
    }
  }
}