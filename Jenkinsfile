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
      }
    }

    stage('Build & Package') {
      steps {
        bat(script: '%workingDir%/%targetTag%/buildscripts/build_engine/build.bat %targetTag% %workingDir% %originTag% ', label: 'Delta Extraction')
        bat(script: '%workingDir%/%targetTag%/buildscripts/build_engine/build_transformation.bat %branchName% %targetTag% %workingDir% %originTag%', label: 'Transformation')
        bat(script: '%workingDir%/%targetTag%/buildscripts/build_engine/build_compilation.bat %branchName% %targetTag% %workingDir% %originTag%', label: 'Compilation')
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