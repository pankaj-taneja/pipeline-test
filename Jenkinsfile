
pipeline {
  agent any
	
  stages {
    stage("Compile") {
      steps {
        sh "./gradlew compileJava"
      }
    }
	  
    stage("Unit test") {
      steps {
        sh "./gradlew test"
      }
    }
	
    stage("Code coverage") {
      steps {
        sh "./gradlew jacocoTestReport"
        publishHTML (target: [
               reportDir: 'build/reports/jacoco/test/html',
               reportFiles: 'index.html',
               reportName: "JaCoCo Report" ])
        sh "./gradlew jacocoTestCoverageVerification"
      }
    }

    stage("Static code analysis") {
      steps {
        sh "./gradlew checkstyleMain"
        publishHTML (target: [
               reportDir: 'build/reports/checkstyle/',
               reportFiles: 'main.html',
               reportName: "Checkstyle Report" ])
      }
    }

    stage('Get Git Tag') {
      steps {
        script {
          GIT_TAG = sh  returnStdout: true, script: '[[ $( git describe --tags $(git rev-parse HEAD) ) =~ .+-[0-9]+-g[0-9A-Fa-f]{6,}$ ]] && echo "" || git describe --tags $(git rev-parse HEAD)'
        }
      }
    }
  
    stage('Compute Docker Tag') {
      steps {
        script {
          buildType = ( env.BRANCH_NAME.split('/')[0] )
          gitTagName = GIT_TAG

          if (buildType in ['develop']) {
              // develop branch will keeps develop as tag name
              // Should we consider latest?
              dockerTag = "latest"
          } else if (buildType in ['master']) {
              // Build master branch as tag. Tag must be the version number.
              // Tag must be present.
              dockerTag = gitTagName
          } else if ( buildType ==~ /PR-.*/ ){
              //   This is a pull request : do everything except push to repo
              dockerTag = buildType
              pullRequest = true
          } else if ( buildType in ['release'] ){
              // BRANCH_NAME : 'release/X.Y.Z' or 'release/X.Y' or 'release/X'
              //   This is a release - either major, feature, fix
              //   Recomended to always use X.Y.Z to make sure we build properly
              version = ( env.BRANCH_NAME.split('/')[1] )
              dockerTag = "${version}-rc"
          } else {
              // */JIRA-###-Description -> JIRA-###
              dockerTag = ( env.BRANCH_NAME.split('/')[1] =~ /.+-\d+/ )[0]
          }
          echo "Computed Docker Tag"
          echo ""
          echo "Doker tag: ${dockerTag}"
          echo ""
          echo "BRANCH NAME: ${BRANCH_NAME}"
          echo "GIT_TAG: ${GIT_TAG}"
        }
      }
    }
/*
    stage("Build") {
      steps {
        sh "./gradlew build"
      }
    }

    stage("Docker build") {
      steps {
        sh "docker build -t leszko/calculator:${BUILD_TIMESTAMP} ."
      }
    }

    stage("Docker login") {
      steps {
        withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'leszko',
                          usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
          sh "docker login --username $USERNAME --password $PASSWORD"
        }
      }
    }

    stage("Docker push") {
      steps {
        sh "docker push leszko/calculator:${BUILD_TIMESTAMP}"
      }
    }

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
