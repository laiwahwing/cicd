pipeline {
  agent any

  options {
    timestamps()
    buildDiscarder(logRotator(numToKeepStr: '30'))
    ansiColor('xterm')
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