#!/usr/bin/env groovy

/*
This is a sample Jenkinsfile to demonstrate the list of stages
that should be implemented for continuous integration and continuous
deployment of a dockerized application.
This pipeline expects following branch names in GitHub repository
that has to be followed:
master
feature/JIRA-ID-Description
fix/JIRA-ID-Description
master
release/X.Y.Z

This pipeline also implements stages for pull requests testing
hence it is strongly recommended to follow pull request practices.

Pipeline follows following Artifact Promotion strategy which in
this case is a docker tag:
Branch Name - feature/JIRA-ID-Description or fix/JIRA-ID-Description
  dockerTag = JIRA-ID (From branch name)
Branch Name - PR-ID
  dockerTag = PR-ID (From PR name)
Branch Name - master (From branch name)
  dockerTag = <module vesion>-<currentBuild number>-dev
After dev test -
  dockerTag = <module vesion>-<currentBuild number>-qa
After QA test -
  dockerTag = <module vesion>-<currentBuild number>-rc
After Acceptance test -
  dockerTag = <module vesion>-<currentBuild number>-prod
*/

pipeline {
  agent any
	environment {
    // Define global environment variables in this section
    buildNum = currentBuild.getNumber()
    buildType = BRANCH_NAME.split('/').first()
    branchVersion = BRANCH_NAME.split('/').last()
    buildVersion = '1.0.0'
  }
  stages {
    stage("Compute Docker Tag") {
      steps {
        script {
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
      }
    }

    stage("Promote Artifact to QA") {
      when {
        expression {
          // Run only for buildTypes master or Release
          buildType in ['master','release']
        }
      }
      steps {
        // supporting components have fixed versions
        echo "Promote Artifact name to module-A:<version>-<build number>-qa"
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
      }
    }

    stage("Promote Artifact to RC") {
      when {
        expression {
          // Run only for buildTypes master or Release
          buildType in ['master','release']
        }
      }
      steps {
        // supporting components have fixed versions
        echo "Promote Artifact name to module-A:<version>-<build number>-rc"
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
      }
    }

    stage("Promote Artifact to PROD") {
      when {
        expression {
          // Run only for buildTypes master or Release
          buildType in ['master','release']
        }
      }
      steps {
        // supporting components have fixed versions
        echo "Promote Artifact name to module-A:<version>-<build number>-prod"
      }
    }
  }
}
