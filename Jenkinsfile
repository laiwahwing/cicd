pipeline {
  agent any

  options {
    buildDiscarder logRotator(
      artifactDaysToKeepStr: "30",
            artifactNumToKeepStr: "100",
            daysToKeepStr: "30",
            numToKeepStr: "50"
        )
  }

  def dpath = ""
  parameters {
    choice(
      description: 'Run what environment?',
      name: 'environment',
      choices: ['Staging', 'Prod']
    )
    choice(
      description: 'Run what package?',
      name: 'package',
      choices: ['biz', 'gateway']
    )
  }

  environment {
    dpath=''
  }

  stages {
    stage("Wat") {
      steps {
        script {
          if(params.environment =="Prod"){
            environment="Production"
            dpath='/data/deploy/${environment}'
          }
          if(params.environment == "Staging"){
            environment="Prerelease"
            dpath='/data/deploy/${environment}'
          }
        }
        echo "selectedEnvironment: ${environment}"
        echo "selected path: ${dpath}"
      }
    }
  }
}