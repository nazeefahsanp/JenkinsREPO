pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
        bat(script: ' %workingDir%/create_build.bat --branchName=%branchName% --targetTag=%targetTag% --originTag="%originTag%" --sourceDir=%workingDir%', label: 'Checkout')
      }
    }

    stage('Code Quality Gate') {
      steps {
        bat(script: '%workingDir%/sonar_scanner.bat', label: 'Code Quality Gate')
        echo 'Notification'
      }
    }

    stage('Build & Package') {
      steps {
        bat(script: '%workingDir%/%targetTag%/buildscripts/build_engine/build.bat %targetTag% %workingDir% %originTag% ', label: 'Delta Extraction')
        bat(script: '%workingDir%/%targetTag%/buildscripts/build_engine/build_transformation.bat %branchName% %targetTag% %workingDir% %originTag%', label: 'Transformation')
        bat(script: '%workingDir%/%targetTag%/buildscripts/build_engine/build_compilation.bat %branchName% %targetTag% %workingDir% %originTag%', label: 'Compilation')
      }
    }

    stage('Deploy') {
      parallel {
        stage('Schema') {
          steps {
            bat(script: '%workingDir%/deploy_build.bat execute.preconfig_scripts', label: 'PreConfig')
            bat(script: '%workingDir%/deploy_build.bat platform_management.configuration', label: 'Platform Management Configuration')
            bat(label: 'Platform Management PnO', script: '%workingDir%/deploy_build.bat  platform_management.PnO')
            bat(label: 'Unified Typing', script: '%workingDir%/deploy_build.bat unified_typing')
            bat(script: '%workingDir%/executeSpinner.bat', label: 'Spinner')
            bat(script: '%workingDir%/deploy_build.bat execute.postconfig_scripts', label: 'PostConfig')
            bat(script: '%workingDir%/deploy_build.bat register.customization', label: 'Compile JPOs')
          }
        }

        stage('Generate WAR') {
          steps {
            bat(label: 'Generate WAR', script: '%workingDir%/deploy_build.bat custom_war')
          }
        }

      }
    }

    stage('Post Deploy') {
      steps {
        bat(script: '%workingDir%/deploy_build.bat import.3DSpaceIndex', label: 'Indexing')
        bat(label: 'Create Output Package', script: '%workingDir%/deploy_build.bat  create.output_dir')
        bat(label: 'Publish WAR', script: '%workingDir%/deploy_build.bat create.output_package')
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