pipeline {
  agent any

  parameters {
    choice(
      description: 'Run what environment?',
      name: 'environment',
      choices: ['PRE', 'PRO']
    )
  }

  stages {
    stage("Wat") {
      steps {
        echo "selectedEnvironment: ${params.environment}"
      }
    }
  }
}