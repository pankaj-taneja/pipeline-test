#!/usr/bin/env groovy

pipeline {
  agent any
	environment {
    // Define global environment variables in this section
    GIT_BRANCH = sh(returnStdout: true, script: 'git rev-parse --abbrev-ref HEAD').trim()
    buildNum = currentBuild.getNumber()
    buildType = GIT_BRANCH.split('/').first()
    branchVersion = GIT_BRANCH.split('/').last()
    buildVersion = '1.0.0'
  }
  stages {
    stage("Compile") {
      when {
        expression {
          // Run only for buildTypes feature or fix or pullRequest
          buildType ==~ /feature.*/ || /PR-.*/ || /fix.*/
        }
      }
      steps {
        sh (echo "Run Commmands to compile code")
      }
    }
    stage("Unit test") {
      when {
        expression {
          // Run only for buildTypes feature or fix or pullRequest
          buildType ==~ /feature.*/ || /PR-.*/ || /fix.*/
        }
      }
      steps {
        sh (echo "Run Commmands to execute unit test")
      }
    }
    stage("Code coverage") {
      when {
        expression {
          // Run only for buildTypes feature or fix or pullRequest
          buildType ==~ /feature.*/ || /PR-.*/ || /fix.*/
        }
      }
      steps {
        sh (echo "Run Commmands to execute code coverage test")
      }
    }
    stage("Code Quality") {
      when {
        expression {
          // Run only for buildTypes feature or fix or pullRequest
          buildType ==~ /feature.*/ || /PR-.*/ || /fix.*/
        }
      }
      steps {
        sh (echo "Run Commmands to execute code quality test")
      }
    }
    stage("Static code analysis") {
      when {
        expression {
          // Run only for buildTypes feature or fix or pullRequest
          buildType ==~ /feature.*/ || /PR-.*/ || /fix.*/
        }
      }
      steps {
        sh (echo "Run Commmands to execute static code analysis test")
      }
    }
    stage("Build") {
      when {
        expression {
          // Run only for buildTypes feature or fix or pullRequest
          buildType ==~ /feature.*/ || /PR-.*/ || /fix.*/
        }
      }
      steps {
        sh (echo "Run Commmands to trigger build")
      }
    }
    stage('Compute Docker tag') {
      steps {
        script {
          if (buildType in ['feature']) {
            // docker image name for feature build - component:<JIRA-ID>
            dockerTag = ( env.GIT_BRANCH.split('/')[1] =~ /.+-\d+/ )[0]
          } else if (buildType ==~ /PR-.*/ ){
            //   This is a pull request
            dockerTag = branchVersion
          } else if (buildType in ['master']) {
              // master branch
              dockerTag = (env.buildVersion-env.buildNum-dev)
          } else if ( buildType in ['release'] ){
              // BRANCH_NAME : 'release/X.Y.Z' or 'release/X.Y' or 'release/X'
              //   This is a release - either major, feature, fix
              //   Recomended to always use X.Y.Z to make sure we build properly
              dockerTag = env.GIT_BRANCH.split('/')[1]
          }
          echo "Computed Docker Tag"
          echo ""
          echo "Docker tag: ${dockerTag}"
          echo ""
          echo "BRANCH NAME: ${GIT_BRANCH}"
          // echo "GIT_TAG: ${GIT_TAG}"
        }
      }
    }

    stage("Docker build") {
      steps {
        // Create docker build with name <module name>:dockerTag
        sh (echo "Run Commmand to trigger docker build - module-A:${dockerTag}")
      }
    }

    stage("Docker push") {
      steps {
        sh (echo "Run Commmand to push docker image in artifactory")
      }
    }
/*
    stage("Deploy to staging") {
      steps {
        sh "ansible-playbook playbook.yml -i inventory/staging"
        sleep 60
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
