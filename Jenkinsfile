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


  parameters {
    choice(
      description: 'Run what environment?',
      name: 'environment',
      choices: ['Staging', 'Prod']
    )
    choice(
      description: 'Run what package?',
      name: 'package',
      choices: ['biz', 'gateway', 'im']
    )
    booleanParam(
      defaultValue: true, 
      description: '', 
      name: 'BOOLEAN'
    )
    text(
      defaultValue: '''
      this is a multi-line 
      string parameter example
      ''', 
        name: 'MULTI-LINE-STRING'
    )
    string(
      defaultValue: 'where\'s my pupet', 
      name: 'gongzai', 
      trim: true
    )
  }

  environment {
    dpath='1'
  }

  stages {
    stage("Wat") {
      steps {
        script {
          if(params.environment == "Prod"){
            environment="Production"
            dpath="/data/deploy/${environment}"
            return dpath
          }
          if(params.environment == "Staging"){
            environment="Prerelease"
            dpath="/data/deploy/${environment}"
            return dpath
          }
        }
        echo "selectedEnvironment: ${environment}"
        echo "selected path: ${dpath}"
      }
    }
  }
}