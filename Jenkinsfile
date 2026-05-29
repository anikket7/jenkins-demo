pipeline
{

    agent any

    tools{
        nodejs "NODE22"
    }

    triggers{
        pollSCM('H/2 * * * *')
    }

    environment{
        DOCKER_IMAGE = "anikket7/jenkins-demo"
        DOCKER_TAG = "latest"
        CONTAINER_NAME = "jenkins-demo-container"
        PORT = "3000"
    }

    stages{
        stage("clone")
        {
            steps{
                git url: "https://github.com/anikket7/jenkins-demo.git",
                branch: "main"
            }
        }

        stage("install dependencies")
        {
            steps{
                sh "npm install"
            }
        }

        stage("build docker image")
        {
            steps{
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }

        stage("push the docker image to docker hub")
        {
            steps{
                withCredentials([
                    usernamePassword(
                        credentialsId:"dockerhub",
                        usernameVariable:"DOCKER_NAME",
                        passwordVariable:"DOCKER_PASSWORD"
                    )
                ]){sh """
                    echo %DOCKER_PASSWORD%| docker login -u %DOCKER_NAME% --password-stdin
                    docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                 """}
            }
        }

        stage("stop the old container")
        {
            steps{
                sh "docker stop ${CONTAINER_NAME}|| true"
            }
        }

        stage("run the new container")
        {
            steps{
                sh "docker run -d -p ${PORT}:${PORT} --name ${CONTAINER_NAME} ${DOCKER_IMAGE}:${DOCKER_TAG}"
            }
        }
    }

    post{
        success{
            echo "Deployment successful!"
        }
    }
}