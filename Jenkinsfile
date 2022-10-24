


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





properties([
  parameters([
    [$class: 'ChoiceParameter', 
      choiceType: 'PT_SINGLE_SELECT', 
      description: 'Select the Environemnt from the Dropdown List', 
      filterLength: 1, 
      filterable: false, 
      name: 'Env', 
      script: [
        $class: 'GroovyScript', 
        fallbackScript: [
            classpath: [], 
            sandbox: false, 
            script: 
                "return['Could not get The environemnts']"
        ], 
        script: [
            classpath: [], 
            sandbox: false, 
            script: 
                "return['dev','stage','prod']"
        ]
     ] 
    ],
    [$class: 'CascadeChoiceParameter', 
        choiceType: 'PT_SINGLE_SELECT', 
        description: 'Select the AMI from the Dropdown List',
        name: 'AMIList', 
        referencedParameters: 'Env', 
        script: 
            [$class: 'GroovyScript',
            fallbackScript: [
              classpath: [],
              sandbox: false,
              script: "return['Could not get Environment from Env Param']"
              ],
            script: [
              classpath: [],
              sandbox: false,
              script: '''
              if (Env.equals("dev")){
                return["ami-sd2345sd", "ami-asdf245sdf", "ami-asdf3245sd"]
              }
              else if(Env.equals("stage")){
                return["ami-sd34sdf", "ami-sdf345sdc", "ami-sdf34sdf"]
              }
              else if(Env.equals("prod")){
                return["ami-sdf34sdf", "ami-sdf34ds", "ami-sdf3sf3"]
              }
                '''
            ] 
        ]
    ],
    [$class: 'DynamicReferenceParameter',
        choiceType: 'ET_ORDERED_LIST',
        description: 'Select the  AMI based on the following information', 
        name: 'ImageInformation',
        referencedParameters: 'Env',
        script:
          [$class: 'GroovyScript',
          script: 'return["Could not get AMi Information"]', 
          script: [
            script: '''
              if (Env.equals("dev")){
                return["ami-sd2345sd:  AMI with Java", "ami-asdf245sdf: AMI with Python", "ami-asdf3245sd: AMI with Groovy"]
              }
              else if(Env.equals("stage")){
                return["ami-sd34sdf:  AMI with Java", "ami-sdf345sdc: AMI with Python", "ami-sdf34sdf: AMI with Groovy"]
              }
              else if(Env.equals("prod")){
                return["ami-sdf34sdf:  AMI with Java", "ami-sdf34ds: AMI with Python", "ami-sdf3sf3: AMI with Groovy"]
              }
              '''
        ]
      ]
   ]
 ])
])
/*
def listSDKVersions() {
  return {
    def folders = sh(script:"ls /tmp", returnStdout:true)
    return folders
  }
}

def SDKVersions = listSDKVersions().call()
choice(name: 'SDK', choices: SDKVersions, description="Select SDKVersions") */

pipeline {
  agent any

  options {
    disableConcurrentBuilds()
    buildDiscarder logRotator(
      artifactDaysToKeepStr: "30",
            artifactNumToKeepStr: "100",
            daysToKeepStr: "30",
            numToKeepStr: "50"
        )
  }

  parameters {
    choice(description: 'Run what environment?', name: 'environment',choices: ['Staging', 'Prod'])
    choice(description: 'Run what package?', name: 'tier',choices: ['web', 'database', 'backend'])
    booleanParam(defaultValue: true, name: 'BuildApp', description: 'Run buildapp or not')
    booleanParam(defaultValue: true, name: 'RunBuild', description: 'Run build or not')
    booleanParam(defaultValue: true, name: 'RunDeploy', description: 'Run deploy or not')
    text(
      defaultValue: '''
      this is a multi-line 
      string parameter example
      ''',  name: 'multiline'
    )
    string(defaultValue: 'where\'s my pupet', name: 'gongzai', trim: true)
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
        echo "selectedEnv: ${params.Environment}"
        echo "selectedEnvironment: ${environment}"
        echo "selectedPath: ${dpath}"
        echo "selectdBuildTier: ${params.Host}"
      }
    }
    stage("BuildApp"){
      when {
        expression { BuildApp }
      }
      steps {
        echo "AMIList: ${AMIList}"
      }
    }
    stage("DeployStaging") {
      when {
        expression { RunDeploy }
      }
      steps {
        buildName "#${BUILD_NUMBER}-${environment}-${dpath}"
        buildDescription "Executed @ ${NODE_NAME}"
        echo "selectedEnvironment: ${environment}"
        echo "Staging Build: selectedLinestring: ${params.multiline}"
      }
    }
  }
}

