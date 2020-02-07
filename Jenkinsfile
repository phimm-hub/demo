// Jenkinsfile
String credentialsId = 'awsCredentials'

try {
  stage('checkout') {
    steps {
      cleanWs()
      checkout scm
    }
  }
  // test shell
  
   stage('test') {
     steps {
     echo 'hello world'
   }
   }
  
  // Run terraform init
  stage('init') {
    steps {
      withCredentials([[
        $class: 'AmazonWebServicesCredentialsBinding',
        credentialsId: credentialsId,
        accessKeyVariable: 'AWS_ACCESS_KEY_ID',
        secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
      ]]) {
        ansiColor('xterm') {
          sh '/usr/local/bin/terraform init'
        }
      }
    }
  }

  // Run terraform plan
  stage('plan') {
    steps {
      withCredentials([[
        $class: 'AmazonWebServicesCredentialsBinding',
        credentialsId: credentialsId,
        accessKeyVariable: 'AWS_ACCESS_KEY_ID',
        secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
      ]]) {
        ansiColor('xterm') {
          sh '/usr/local/bin/terraform plan'
        }
      }
    }
  }

  if (env.BRANCH_NAME == 'master') {

    // Run terraform apply
    stage('apply') {
      steps {
        withCredentials([[
          $class: 'AmazonWebServicesCredentialsBinding',
          credentialsId: credentialsId,
          accessKeyVariable: 'AWS_ACCESS_KEY_ID',
          secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
        ]]) {
          ansiColor('xterm') {
            sh '/usr/local/bin/terraform apply -auto-approve'
          }
        }
      }
    }

    // Run terraform show
    stage('show') {
      steps {
        withCredentials([[
          $class: 'AmazonWebServicesCredentialsBinding',
          credentialsId: credentialsId,
          accessKeyVariable: 'AWS_ACCESS_KEY_ID',
          secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
        ]]) {
          ansiColor('xterm') {
            sh '/usr/local/bin/terraform show'
            sh '/usr/local/bin/terraform output instance_ips >> instanceip.txt'
          }
        }
      }
    }
    
   // test shell
  
   stage('deploy app to host') {
     steps {
     sh TESTVAR="$(cat instanceip.txt |grep -oE "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b")"
     sh echo $TESTVAR
   }
   }
    
  }
  currentBuild.result = 'SUCCESS'
}
catch (org.jenkinsci.plugins.workflow.steps.FlowInterruptedException flowError) {
  currentBuild.result = 'ABORTED'
}
catch (err) {
  currentBuild.result = 'FAILURE'
  throw err
}
finally {
  if (currentBuild.result == 'SUCCESS') {
    currentBuild.result = 'SUCCESS'
  }
}
