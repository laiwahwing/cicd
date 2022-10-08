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
        if(environment.equals("Prod"))
          echo "selectedEnvironment: ${params.environment}"
        else if(environment.equals("Pre"))
          echo "selectedEnvironment: ${params.environment}"
      }
    }
  }
}