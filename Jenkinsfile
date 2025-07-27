pipeline {
  agent any

  environment {
    IMAGE_NAME = "my-go-app"
    REGISTRY = "your-registry.example.com" // 如果没有镜像仓库，可以先用本地
  }

  stages {
    stage('Checkout') {
      steps {
        echo "📥 拉取代码..."
        git url: 'https://github.com/zy2d/my-docker-test01.git', branch: 'main'
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          GIT_COMMIT_ID = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
          IMAGE_TAG = "${IMAGE_NAME}:${GIT_COMMIT_ID}"
          echo "🔨 构建镜像: ${IMAGE_TAG}"
          sh "docker build -t ${IMAGE_TAG} ."
        }
      }
    }

    stage('Optional Push') {
      when {
        expression { return false } // 如果你暂时不推送镜像，可以保留这个
      }
      steps {
        script {
          sh "docker tag ${IMAGE_TAG} ${REGISTRY}/${IMAGE_TAG}"
          sh "docker push ${REGISTRY}/${IMAGE_TAG}"
        }
      }
    }
  }

  post {
    success {
      echo "✅ 构建成功，镜像已打包为 ${IMAGE_TAG}"
    }
    failure {
      echo "❌ 构建失败，请检查日志"
    }
  }
}
