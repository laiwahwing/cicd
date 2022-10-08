pipeline {
  agent any

  options {
    timestamps()
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
        if(${params.environment}.equals("Prod"))
          environment="Production"
        else if(${params.environment}.equals("Pre"))
          environment="Prerelease"
        echo "selectedEnvironment: ${environment}"
      }
    }
  }
}