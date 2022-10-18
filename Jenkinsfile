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
      name: 'tier',
      choices: ['web', 'database', 'backend']
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
        name: 'multiline'
    )
    string(
      defaultValue: 'where\'s my pupet', 
      name: 'gongzai', 
      trim: true
    )
    choice(name: 'ServiceTier', choices: ServiceTier, description="Select ServiceTier")
  }

  environment {
    dpath='1'
  }

  stages {
    stage("InitEnvironment") {
      steps {
        script {
          if(params.environment == "Prod"){
            environment="Production"
            dpath="/data/deploy/${environment}"
          }
          if(params.environment == "Staging"){
            environment="Prerelease"
            dpath="/data/deploy/${environment}"
          }
        }
        echo "selectedEnvironment: ${environment}"
        echo "selectedPath: ${dpath}"
      }
    }
    stage("Build") {
      steps {
        echo "selectedEnvironment: ${environment}"
        echo "selectedPath: ${dpath}"
        echo "selectdBuildTier: ${service.service_name}"
        echo "selectdTag: ${service.release_tag}"
      }
    }
    stage("DeployStaging") {
      when {
        expression { environment=='Prerelease' }
      }
      steps {
        echo "selectedEnvironment: ${environment}"
        echo "Staging Build: selectedLinestring: ${params.multiline}"
      }
    }
  }
}


def ServiceTier = getDynamicParameter().call()

def getDynamicParameter() {

  service_tier_map = [
    "web": [
      ["service_name": "user_frontend", "release_tag": "1.0.0" ],
      ["service_name": "admin_frontend", "release_tag": "1.0.2" ],
    ],
    "backend": [
      ["service_name": "admin_service", "release_tag": "2.1.0" ],
      ["service_name": "finance_service", "release_tag": "2.2.0" ],
      ["service_name": "payment_service", "release_tag": "3.2.0" ],
    ],
    "database": [
      ["service_name": "dynamo_db", "release_tag": "5.4.1"],
      ["service_name": "mysql", "release_tag": "3.2.1"],
      ["service_name": "postgresql", "release_tag": "1.2.3"],
    ],
  ]

  html_to_be_rendered = "<table><tr>"
  service_list = service_tier_map[tier]
  service_list.each { service ->
    html_to_be_rendered = """
      ${html_to_be_rendered}
      <tr>
      <td>
      <input name=\"value\" alt=\"${service.service_name}\" json=\"${service.service_name}\" type=\"checkbox\" class=\" \">
      <label title=\"${service.service_name}\" class=\" \">${service.service_name}</label>
      </td>
      <td>
      <input type=\"text\" class=\" \" name=\"value\" value=\"${service.release_tag}\"> </br>
      </td>
      </tr>
  """
  }


  html_to_be_rendered = "${html_to_be_rendered}</tr></table>"

  return html_to_be_rendered
}