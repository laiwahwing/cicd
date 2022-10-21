


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




/*
properties([
  parameters([
    choice(description: 'Run what environment?', name: 'environment',choices: ['Staging', 'Prod']),
    choice(description: 'Run what package?', name: 'tier',choices: ['web', 'database', 'backend']),
    booleanParam(defaultValue: true, name: 'RunBuild', description: 'Run build or not',),
    booleanParam(defaultValue: true, name: 'RunDeploy', description: 'Run deploy or not',  ),
    text(
      defaultValue: '''
      this is a multi-line 
      string parameter example
      ''',  name: 'multiline'
    ),
    string(defaultValue: 'where\'s my pupet', name: 'gongzai', trim: true),
    [
      $class: 'ChoiceParameter',
      choiceType: 'PT_SINGLE_SELECT',
      filterLength: 1,
      filterable: true,
      description: 'select the env name from the dropdown list',
      name: 'Environment',
      randomName: 'choice-parameter-5631314439613978',
      script: [
        $class: 'GroovyScript',
        fallbackScript: [
          classpath: [],
          sanbox: false,
          script:
            'return[\'Could not get Env\']'
        ],
        script: [
          classpath: [],
          sanbox: false,
          script:
            'return["Dev", "QA", "Stage", "Prod"]'
        ]
      ]
    ],
    [
      $class: 'CascadeChoiceParameter',
      choiceType: 'PT_SINGLE_SELECT',
      filterLength: 1,
      filterable: true,
      name: 'Host',
      referencedParameters: 'Environment',
      randomName: 'choice-parameter-5631314456178619', 
      script: [
        $class: 'GroovyScript',
        fallbackScript: [
          classpath: [], 
          sandbox: false, 
          script: 
            'return[\'Could not get Environment from Env Param\']'
          ], 
        script: [
            classpath: [], 
            sandbox: false, 
            script: 
              ''' if (Environment.equals("Dev")){
                      return["devaaa001","devaaa002","devbbb001","devbbb002","devccc001","devccc002"]
                  }
                  else if(Environment.equals("QA")){
                      return["qaaaa001","qabbb002","qaccc003"]
                  }
                  else if(Environment.equals("Stage")){
                      return["staaa001","stbbb002","stccc003"]
                  }
                  else if(Environment.equals("Prod")){
                      return["praaa001","prbbb002","prccc003"]
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
    booleanParam(defaultValue: true, name: 'RunBuild', description: 'Run build or not')
    booleanParam(defaultValue: true, name: 'RunDeploy', description: 'Run deploy or not')
    activeChoiceParam('choice1') {
      description('select your choice')
      choiceType('PT_SINGLE_SELECT')
      groovyScript {
          script('return["aaa","bbb"]')
          fallbackScript('return ["error"]')
      }
    }
    activeChoiceReactiveParam('choice2') {
      description('select your choice')
      choiceType('PT_SINGLE_SELECT')
      groovyScript {
          script(' if(choice1.equals("aaa")) { return ["a-branch", "a-target"] } else {return ["b-branch","b-target"] } ')
          fallbackScript('return ["error"]')
      }
      referencedParameter('choice1')
    }
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

