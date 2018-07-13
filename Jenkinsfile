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
  }
  stages {
    stage("Compute Docker Tag") {
      steps {
        if (buildType in ['feature','fix']) {
          // docker image name for feature build - component:<JIRA-ID>
          env.dockerTag = ( env.BRANCH_NAME.split('/')[1] =~ /.+-\d+/ )[0]
        } else if (buildType ==~ /PR-.*/ ){
          //   This is a pull request
          env.dockerTag = buildType
        } else if (buildType in ['master']) {
          // master branch
          env.dockerTag = env.buildVersion-env.buildNum-dev
        } else if ( buildType in ['release'] ){
          // BRANCH_NAME : 'release/X.Y.Z' or 'release/X.Y' or 'release/X'
          //   This is a release - either major, feature, fix
          //   Recomended to always use X.Y.Z to make sure we build properly
          env.dockerTag = env.BRANCH_NAME.split('/')[1]
        }
      }
    }
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
        echo "Run Commmand to trigger docker build - module-A:${env.dockerTag}"
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
      }
      steps {
        // supporting components have fixed versions
        echo "Deploy the Artifact"
        echo "Trigger test run to verify integration"
        echo "ReTag the artifact to module-A:<version>-<build number>-qa"
      }
    }

    stage("Deploy and test on the QA environment") {
      when {
        expression {
          // Run only for buildTypes master or Release
          buildType in ['master','release']
        }
      }
      steps {
        // supporting components have fixed versions
        echo "Deploy the Artifact"
        echo "Trigger test run to verify integration testing"
        echo "ReTag the artifact to module-A:<version>-<build number>-qa"
      }
    }

    stage("Deploy and test on the Pre-Prod environment") {
      when {
        expression {
          // Run only for buildTypes master or Release
          buildType in ['master','release']
        }
      }
      steps {
        // supporting components have fixed versions
        echo "Deploy the Artifact"
        echo "Trigger test run to verify Acceptance testing"
        echo "ReTag the artifact to module-A:<version>-<build number>-qa"
      }
    }
  }
}
