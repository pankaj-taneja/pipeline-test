#!/usr/bin/env groovy

pipeline {
  agent any
	environment {
    // Define global environment variables in this section
    // GIT_BRANCH = sh(returnStdout: true, script: 'git rev-parse --abbrev-ref HEAD').trim()
    buildNum = currentBuild.getNumber()
    buildType = BRANCH_NAME.split('/').first()
    branchVersion = BRANCH_NAME.split('/').last()
    buildVersion = '1.0.0'
    if (buildType in ['feature','fix']) {
      // docker image name for feature build - component:<JIRA-ID>
      dockerTag = ( env.BRANCH_NAME.split('/')[1] =~ /.+-\d+/ )[0]
      echo "Run Commmand to trigger docker build - module-A:${dockerTag}"
    } else if (buildType ==~ /PR-.*/ ){
      //   This is a pull request
      dockerTag = buildType
      echo "Run Commmand to trigger docker build - module-A:${dockerTag}"
    } else if (buildType in ['master']) {
      // master branch
      dockerTag = env.buildVersion-env.buildNum-dev
      echo "Run Commmand to trigger docker build - module-A:${dockerTag}"
    } else if ( buildType in ['release'] ){
      // BRANCH_NAME : 'release/X.Y.Z' or 'release/X.Y' or 'release/X'
      //   This is a release - either major, feature, fix
      //   Recomended to always use X.Y.Z to make sure we build properly
      dockerTag = env.BRANCH_NAME.split('/')[1]
      echo "Run Commmand to trigger docker build - module-A:${dockerTag}"
      }
  }
  stages {
    stage("Compile") {
      when {
        expression {
          // Run only for buildTypes feature or fix or pullRequest
          buildType ==~ /feature.*/ || /PR-.*/ || /fix.*/ || master
        }
      }
      steps {
        echo "Run Commmands to compile code"
      }
    }
    stage("Unit test") {
      when {
        expression {
          // Run only for buildTypes feature or fix or pullRequest
          buildType ==~ /feature.*/ || /PR-.*/ || /fix.*/ || master
        }
      }
      steps {
        echo "Run Commmands to execute unit test"
      }
    }
    stage("Code coverage") {
      when {
        expression {
          // Run only for buildTypes feature or fix or pullRequest
          buildType ==~ /feature.*/ || /PR-.*/ || /fix.*/ || master
        }
      }
      steps {
        echo "Run Commmands to execute code coverage test"
      }
    }
    stage("Code Quality") {
      when {
        expression {
          // Run only for buildTypes feature or fix or pullRequest
          buildType ==~ /feature.*/ || /PR-.*/ || /fix.*/ || master
        }
      }
      steps {
        echo "Run Commmands to execute code quality test"
      }
    }
    stage("Static code analysis") {
      when {
        expression {
          // Run only for buildTypes feature or fix or pullRequest
          buildType ==~ /feature.*/ || /PR-.*/ || /fix.*/ || master
        }
      }
      steps {
        echo "Run Commmands to execute static code analysis test"
      }
    }
    stage("Build") {
      when {
        expression {
          // Run only for buildTypes feature or fix or pullRequest
          buildType ==~ /feature.*/ || /PR-.*/ || /fix.*/ || master
        }
      }
      steps {
        echo "Run Commmands to trigger build"
      }
    }
    stage("print vars") {
      steps {
        echo "buildType: ${buildType}"
        echo "BRANCH_NAME: ${BRANCH_NAME}"
      }
    }
    stage('Create Docker Image') {
      steps {
        script {
          if (buildType in ['feature','fix']) {
            // docker image name for feature build - component:<JIRA-ID>
            dockerTag = ( env.BRANCH_NAME.split('/')[1] =~ /.+-\d+/ )[0]
            echo "Run Commmand to trigger docker build - module-A:${dockerTag}"
          }
          else if (buildType ==~ /PR-.*/ ){
            //   This is a pull request
            dockerTag = buildType
            echo "Run Commmand to trigger docker build - module-A:${dockerTag}"
          }
          else if (buildType in ['master']) {
            // master branch
            dockerTag = env.buildVersion-env.buildNum-dev
            echo "Run Commmand to trigger docker build - module-A:${dockerTag}"
          }
          else if ( buildType in ['release'] ){
            // BRANCH_NAME : 'release/X.Y.Z' or 'release/X.Y' or 'release/X'
            //   This is a release - either major, feature, fix
            //   Recomended to always use X.Y.Z to make sure we build properly
            dockerTag = env.BRANCH_NAME.split('/')[1]
            echo "Run Commmand to trigger docker build - module-A:${dockerTag}"
          }
        }
      }
    }

    stage("Docker push") {
      steps {
        echo "Run Commmand to push docker image in artifactory"
      }
    }

    stage("Deploy and test on the dev environment") {
      when {
        expression {
          // Run only for buildTypes master or Release
          buildType in ['master','release']
        }
      steps {
        // supporting components have fixed versions
        echo "Deploy the Artifact"
        echo "Trigger test run to verify integration"
        echo "ReTag the artifact to module-A:<version>-<build number>-qa"
      }
    }

    stage("Acceptance test") {
      steps {
	sh "./acceptance_test.sh 192.168.0.166"
      }
    }

    // Performance test stages

    stage("Release") {
      steps {
        sh "ansible-playbook playbook.yml -i inventory/production"
        sleep 60
      }
    }

    stage("Smoke test") {
      steps {
	sh "./smoke_test.sh 192.168.0.115"
      }
    } */
  }
}
