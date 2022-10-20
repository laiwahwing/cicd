
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


def listSDKVersions() {
  return {
    node('Linux'){
      def folders = sh(script:"ls /tmp", returnStdout:true)
      return folders
    }
  }
}



properties([
  parameters([
    [
      $class: 'ChoiceParameter',
      choiceType: 'PT_SINGLE_SELECT',
      name: 'Environment',
      script: [
        $class: 'ScriptlerScript',
        scriptlerScriptId:'Environments.groovy'
      ]
    ],
    [
      $class: 'CascadeChoiceParameter',
      choiceType: 'PT_SINGLE_SELECT',
      name: 'Host',
      referencedParameters: 'Environment',
      script: [
        $class: 'ScriptlerScript',
        scriptlerScriptId:'HostsInEnv.groovy',
        parameters: [
          [name:'Environment', value: '$Environment']
        ]
      ]
   ]
 ])
])

def SDKVersions = listSDKVersions().call()

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
      name: 'RunBuild'
    )
    booleanParam(
      defaultValue: true, 
      description: 'Run deploy or not?', 
      name: 'RunDeploy'
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
    /* choice(name: 'SDK', choices: SDKVersions, description="Select SDKVersions") */
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
        expression { RunDeploy }
      }
      steps {
        buildName '#${BUILD_NUMBER}-${environment}-${dpath}'
        buildDescription "Executed @ ${NODE_NAME}"
        echo "selectedEnvironment: ${environment}"
        echo "Staging Build: selectedLinestring: ${params.multiline}"
      }
    }
  }
}

