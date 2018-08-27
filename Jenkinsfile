#!/usr/bin/env groovy

/*
This is a sample Jenkinsfile to demonstrate the list of stages
that should be implemented for continuous integration and continuous
deployment of a RPM artifact.
This pipeline expects following branch names in GitHub repository
that has to be followed:
master
feature/JIRA-ID-Description
fix/JIRA-ID-Description
release/X.Y.Z

This pipeline also implements stages for pull requests testing
hence it is strongly recommended to follow pull request practices.
Which is: Nothing can be merged into master or release branch without
creating a pull request.

Pipeline follows following Artifact versioning strategy which in
this case is a RPM package promotion to different repos in artifactory:
If Branch Name is feature/JIRA-ID-Description or fix/JIRA-ID-Description
  then, RPMVersion = JIRA-ID (From branch name, ephemeral Artifact) which is to
  be pushed in application-repo-name/ephemeral repo.
If Branch Name is PR-ID
  then, RPMVersion = PR-ID (From PR name, ephemeral Artifact) which is to
  be pushed in application-repo-name/ephemeral repo.
If Branch Name is master or release,
  then, RPMVersion = <module vesion>-<currentBuild number> which is to be
  pushed in application-repo-name/dev repo.
After dev test, same rpm to be copied to application-repo-name/qa repo.
After QA test, same rpm to be copied to application-repo-name/rc repo.
After Acceptance test, same rpm to be copied to application-repo-name/prod repo.

* Ephemeral artifacts created with feature or fix Branches
 and with pull should get deleted after closure of the
 branch/PR.
* Note that docker tags promotion on master or release branches
does not create separate artifacts but a different tag to
the same docker image which should be kept until the image is
in use.
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
            // docker tag for a feature or fix branch
            env.rpmRelease = ( env.BRANCH_NAME.split('/')[1] =~ /.+-\d+/ )[0]
          } else if (buildType ==~ /PR-.*/ ){
            // docker tag for a pull request
            env.rpmRelease = buildType
          } else if (buildType in ['master']) {
            // docker tag for a master branch
            env.rpmReleaseVersion = "${env.buildVersion}-${env.buildNum}"
            env.rpmReleaseStage = "dev"
            env.rpmRelease = "${env.rpmReleaseVersion}-${env.rpmReleaseStage}"
          } else if ( buildType in ['release'] ){
            // docker tag for a release branch
            env.rpmReleaseVersion = "${env.branchVersion}-${env.buildNum}"
            env.rpmReleaseStage = "dev"
            env.rpmRelease = "${env.rpmReleaseVersion}-${env.rpmReleaseStage}"
          }
        }
      }
    }
    stage("Build and test") {
      parallel {
        stage("Compile") {
          steps {
            echo "Run Commmands to compile code"
            sh "touch sample.rpm"
          }
        }
        stage("Unit test") {
          steps {
            echo "Run Commmands to execute unit test"
          }
        }
        stage("Code coverage") {
          steps {
            echo "Run Commmands to execute code coverage test"
          }
        }
        stage("Code Quality") {
          steps {
            echo "Run Commmands to execute code quality test"
          }
        }
        stage("Static code analysis") {
          steps {
            echo "Run Commmands to execute static code analysis test"
          }
        }
      }
    }
    stage("Build") {
      steps {
        echo "Run Commmands to trigger build"
      }
    }
    stage('Create Docker Image') {
      steps {
        echo "Run Commmand to trigger docker build - module-A:${env.rpmRelease}"
      }
    }

    stage("Push docker images in artifactory") {
      steps {
        echo "Run Commmand to push docker image in artifactory"

      }
    }

    stage("Deploy on feature/fix/PR ephemeral test environments") {
      when {
        expression {
          env.buildType ==~ /(feature|PR-.*|fix)/
        }
      }
      steps {
        // Stubs should be used to perform functional testing
        echo "Deploy the Artifact on ephemeral environment"
        echo "Trigger test run to verify code changes"
      }
    }

    stage("Deploy on artifactory for them if master or release") {
      when {
        expression {
          // Run only for buildTypes master or Release
          env.buildType in ['master','release']
        }
      }
      steps {
        // supporting components have fixed versions
        echo "Deploy the Artifact on dev setup"
        echo "Trigger integration test run with fixed versions"
        sh  "curl --user dev-deployer:dev@guavus -X PUT 'artifacts.ggn.in.guavus.com:/ggn-dev-rpms/gvs-accelerators/cdap-plugins/master/${env.rpmRelease}/' -T ./sample.rpm"
      }
    }

    stage("Promote Artifact to QA") {
      when {
        expression {
          // Run only for buildTypes master or Release
          env.buildType in ['master','release']
        }
      }
      environment {
        rpmReleaseStage = "qa"
        rpmRelease = "${env.rpmReleaseVersion}-${rpmReleaseStage}"
      }
      steps {
        echo "Promote Artifact name to module-A:${rpmRelease} in artifactory"
      }
    }

    stage("Deploy and test on the QA environment") {
      when {
        expression {
          // Run only for buildTypes master or Release
          env.buildType in ['master','release']
        }
      }
      steps {
        echo "Deploy the Artifact on QA setup"
        echo "Trigger integration test run with latest versions"
      }
    }

    stage("Promote Artifact to RC") {
      when {
        expression {
          // Run only for buildTypes master or Release
          env.buildType in ['master','release']
        }
      }
      environment {
        rpmReleaseStage = "rc"
        rpmRelease = "${env.rpmReleaseVersion}-${rpmReleaseStage}"
      }
      steps {
        // supporting components have fixed versions
        echo "Promote Artifact name to module-A:${rpmRelease}"
      }
    }

    stage("Deploy and test on the Pre-Prod environment") {
      when {
        expression {
          // Run only for buildTypes master or Release
          env.buildType in ['master','release']
        }
      }
      steps {
        // supporting components have fixed versions
        echo "Deploy the Artifact on staging equivalent setup"
        echo "Trigger integration test run with latest stable versions"
      }
    }

    stage("Promote Artifact to PROD") {
      when {
        expression {
          // Run only for buildTypes master or Release
          env.buildType in ['master','release']
        }
      }
      environment {
        rpmReleaseStage = "prod"
        rpmRelease = "${env.rpmReleaseVersion}-${rpmReleaseStage}"
      }
      steps {
        // supporting components have fixed versions
        echo "Promote Artifact name to module-A:${rpmRelease}"
      }
    }
  }
}
