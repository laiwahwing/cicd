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
      choices: ['Pre', 'Prod']
    )
  }

  stages {
    stage("Wat") {
      steps {
        script {
          if('${params.environment}' =="Prod"){
            environment="Production"
            dpath='/data/deploy/${environment}'
          }
          if('${params.environment}' == "Pre"){
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