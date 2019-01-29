pipeline {
  parameters {
    string (name: 'DOCKER_CRED', defaultValue: params.DOCKER_CRED ?: '', description: 'credentialsId for Docker registry in Jenkins')
    string (name: 'DOCKER_REPO', defaultValue: params.DOCKER_REPO ?: '', description: 'Docker repository')
    string (name: 'DOCKER_REG', defaultValue: params.DOCKER_REG ?: '', description: 'Docker registry')
    string (name: 'JENKINS_AGENT', defaultValue: params.JENKINS_AGENT ?: '', description: 'Jenkins agent to use')
  }

  agent { node { label "${JENKINS_AGENT}" } }
  stages {
      stage('Build docker image') {
        steps {
          echo "Building ${DOCKER_REPO} docker image..."
          sh "docker build -t ${DOCKER_REG}/${DOCKER_REPO}:${BUILD_NUMBER} ."
        }
      }
      stage('Push docker image') {
        steps {
          echo "Pushing ${DOCKER_REPO} docker image..."

          withCredentials([[$class: 'UsernamePasswordMultiBinding',
                             credentialsId: "${DOCKER_CRED}",
                             usernameVariable: "DOCKER_USR",
                             passwordVariable: "DOCKER_PWD"]]) {
            sh "docker login -u ${DOCKER_USR} -p ${DOCKER_PWD} ${DOCKER_REG}"
          }
          sh "docker push ${DOCKER_REG}/${DOCKER_REPO}:${BUILD_NUMBER}"
          sh "docker tag ${DOCKER_REG}/${DOCKER_REPO}:${BUILD_NUMBER} ${DOCKER_REG}/${DOCKER_REPO}:latest"
          sh "docker push ${DOCKER_REG}/${DOCKER_REPO}:latest"
        }
      }
  }
}
