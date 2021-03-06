pipeline {
  agent {
    docker {
      image 'px4io/px4-dev-simulation:2017-10-23'
      args '--env CCACHE_DISABLE=1 --env CI=true'
    }
  }
  stages {
    stage('Quality Checks') {
      steps {
        sh 'make check_format'
      }
    }
    stage('Build') {
          steps {
            sh 'make nuttx_px4fmu-v2_default'
            archiveArtifacts 'build/*/*.px4'
          }
    }
    stage('Test') {
      steps {
        sh 'make posix_sitl_default test_results_junit'
        junit 'build/posix_sitl_default/JUnitTestResults.xml'
      }
    }
    stage('Generate Metadata') {
      parallel {
        stage('airframe') {
          steps {
            sh 'make airframe_metadata'
            archiveArtifacts 'airframes.md, airframes.xml'
          }
        }
        stage('parameters') {
          steps {
            sh 'make parameters_metadata'
            archiveArtifacts 'parameters.md, parameters.xml'
          }
        }
        stage('modules') {
          steps {
            sh 'make module_documentation'
            archiveArtifacts 'modules/*.md'
          }
        }
      }
    }
    stage('S3 Upload') {
      when {
        branch 'master|beta|stable'
      }
      steps {
        sh 'echo "uploading to S3"'
      }
    }
  }
}
