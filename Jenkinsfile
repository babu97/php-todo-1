pipeline {
  agent any

  environment {
    TAG = "${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
    DOCKER_CREDS = credentials('docker_creds')
  }

  stages {
    stage("Initial cleanup") {
      steps {
        dir("${WORKSPACE}") {
          deleteDir()
        }
      }
    }

    stage('Checkout SCM') {
      steps {
        git branch: '$BRANCH_NAME', url: 'https://github.com/toritsejuFO/php-todo.git'
      }
    }

    stage('Docker login') {
      steps {
        script {
          sh 'docker login -u ${DOCKER_CREDS_USR} -p ${DOCKER_CREDS_PSW}'
        }
      }
    }

    stage('Docker build') {
      steps {
        script {
          sh "docker build -t php-todo:${TAG} ."
        }
      }
    }

    stage('Docker run') {
      steps {
        script {
          sh "docker run --name php-todo-${env.BUILD_ID} -p 8000:8000 -d php-todo:${TAG}"
        }
      }
    }

    stage('Run test') {
      steps {
        script {
          sh "sleep 15"
          sh "echo Running test now"
          sh "curl -I localhost:8000"
        }
      }
    }

    stage('Tag image') {
      steps {
        script {
          sh "docker tag php-todo:${TAG} toritseju/php-todo:${TAG}"
        }
      }
    }

    stage('Push image') {
      steps {
        script {
          sh "docker push toritseju/php-todo:${TAG}"
        }
      }
    }

    stage('Clean up') {
      steps {
        script {
          sh "docker stop php-todo-${env.BUILD_ID}"
          sh "docker rm php-todo-${env.BUILD_ID}"
          sh "docker rmi php-todo:${TAG}"
          sh "docker rmi toritseju/php-todo:${TAG}"
        }
      }
    }

    stage('Docker logout') {
      steps {
        script {
          sh "docker logout"
        }
      }
    }
  }
}