pipeline {
    agent any
    stages {
        stage('检出') {
            steps {
                checkout([$class           : 'GitSCM',
                  branches         : [[name: GIT_BUILD_REF]],
                  userRemoteConfigs: [[
                      url          : GIT_REPO_URL,
                      credentialsId: CREDENTIALS_ID
                  ]]]
                )
            }
        }

        stage('构建基础镜像并推送到 CODING Docker 制品库') {
            steps {
                sh "docker build -t ${CODING_DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_VERSION} -f ${DOCKERFILE_PATH} ${DOCKER_BUILD_CONTEXT}"
                sh "docker tag  ${CODING_DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_VERSION} ${CODING_DOCKER_REG_HOST}/${CODING_DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_VERSION}"
                sh "docker login -u ${PROJECT_TOKEN_GK} -p ${PROJECT_TOKEN} ${CODING_DOCKER_REG_HOST}"
                sh "docker push ${CODING_DOCKER_REG_HOST}/${CODING_DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_VERSION}"
                // useCustomStepPlugin(key: 'coding-public:artifact_docker_push', version: 'latest', params: [image:"${CODING_DOCKER_IMAGE_NAME}:latest",repo:"${DOCKER_REPO_NAME}"])
            }
        }

    }
    environment {
        DOCKER_REPO_NAME = 'base-image'
        DOCKERFILE_PATH = 'Dockerfile-openjdk-${DOCKER_IMAGE_VERSION}'
        DOCKER_BUILD_CONTEXT = '.'
        DOCKER_IMAGE_VERSION = '8-jdk-alpine'
        CODING_DOCKER_REG_HOST = "${CCI_CURRENT_TEAM}-docker.pkg.${CCI_CURRENT_DOMAIN}"
        CODING_DOCKER_IMAGE_NAME = "${PROJECT_NAME.toLowerCase()}/${DOCKER_REPO_NAME}/openjdk"
    }
}
