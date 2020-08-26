pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
        bat(script: 'echo ""', label: 'Checkout')
      }
    }

    stage('Code Quality Gate') {
      steps {
        bat(script: 'echo ""', label: 'Code Quality Gate')
        echo 'Notification'
      }
    }

    stage('Build & Package') {
      steps {
        echo 'test'
      }
    }

    stage('Deploy') {
      parallel {
        stage('Schema') {
          steps {
            bat(script: 'echo ""', label: 'Compile JPOs')
          }
        }

        stage('Generate WAR') {
          steps {
            bat(label: 'Generate WAR', script: 'echo ""')
          }
        }

      }
    }

    stage('Post Deploy') {
      steps {
        bat(script: 'echo ""', label: 'Indexing')
      }
    }

    stage('Functional Quality Gate') {
      parallel {
        stage('Automated Regression Tests') {
          steps {
            bat(script: '%workingDir%/automated_Regression_test.bat', label: 'Automated Regression Tests')
          }
        }

        stage('Automated Feature Tests') {
          steps {
            bat(script: '%workingDir%/automated_Feature_test.bat', label: 'Automated Feature Tests')
          }
        }

        stage('Automated IPEC Tests') {
          steps {
            bat(script: '%workingDir%/automated_IPEC_TEST.bat', label: 'Automated IPEC Tests')
          }
        }

      }
    }

  }
  environment {
    branchName = 'master'
    targetTag = '20200630120006'
    originTag = '20200630120005'
    workingDir = 'C:\\Apps\\jenkins\\workspace\\PWC_3DEx_Build_Package'
  }
}