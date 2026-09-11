pipeline {
    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    triggers {
        githubPush()
    }

    environment {
        AWS_REGION      = 'eu-west-1'
        ECR_REGISTRY    = '064990711811.dkr.ecr.eu-west-1.amazonaws.com'
        ECR_REPOSITORY  = 'easyshop'
        EKS_CLUSTER     = 'easyshop-eks'
        K8S_NAMESPACE   = 'easyshop'
        KUBECONFIG      = "${WORKSPACE}/kubeconfig"
        IMAGE_TAG       = "${BUILD_NUMBER}"
        ECR_IMAGE       = "${ECR_REGISTRY}/${ECR_REPOSITORY}:${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm ci'
            }
        }

        stage('Lint') {
            steps {
                sh 'npm run lint'
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('Sonar') {
                    script {
                        def scannerHome = tool 'SonarQube Scanner'

                        sh """
                            ${scannerHome}/bin/sonar-scanner
                        """
                    }
                }
            }
        }

        stage('Trivy Filesystem Scan') {
            steps {
                sh '''
                    trivy fs \
                      --scanners vuln,secret \
                      --severity HIGH,CRITICAL \
                      --exit-code 0 \
                      .
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    docker build \
                      -t ${ECR_REPOSITORY}:${BUILD_NUMBER} \
                      .
                '''
            }
        }

        stage('Login to Amazon ECR') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'aws-ecr']
                ]) {
                    sh '''
                        aws ecr get-login-password \
                          --region ${AWS_REGION} |
                        docker login \
                          --username AWS \
                          --password-stdin ${ECR_REGISTRY}
                    '''
                }
            }
        }

        stage('Tag Docker Image') {
            steps {
                sh '''
                    docker tag \
                      ${ECR_REPOSITORY}:${BUILD_NUMBER} \
                      ${ECR_IMAGE}
                '''
            }
        }

        stage('Push Image to ECR') {
            steps {
                sh '''
                    docker push ${ECR_IMAGE}
                '''
            }
        }

        stage('Configure EKS Access') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'aws-ecr']
                ]) {
                    sh '''
                        aws eks update-kubeconfig \
                          --region ${AWS_REGION} \
                          --name ${EKS_CLUSTER} \
                          --kubeconfig ${KUBECONFIG}

                        export KUBECONFIG=${KUBECONFIG}

                        echo "===== EKS CLUSTER ====="
                        kubectl cluster-info

                        echo "===== EKS NODES ====="
                        kubectl get nodes
                    '''
                }
            }
        }

        stage('Deploy Kubernetes Resources') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'aws-ecr']
                ]) {
                    sh '''
                        export KUBECONFIG=${KUBECONFIG}

                        echo "===== APPLY DEPLOYMENT ====="
                        kubectl apply \
                          -f k8s-deployment.yaml

                        echo "===== APPLY SERVICE ====="
                        kubectl apply \
                          -f easyshop-service.yaml

                        echo "===== APPLY HPA ====="
                        kubectl apply \
                          -f easyshop-hpa.yaml

                        echo "===== APPLY INGRESS ====="
                        kubectl apply \
                          -f easyshop-ingress.yaml
                    '''
                }
            }
        }

        stage('Deploy New Image') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'aws-ecr']
                ]) {
                    sh '''
                        export KUBECONFIG=${KUBECONFIG}

                        echo "===== UPDATE IMAGE ====="

                        kubectl -n ${K8S_NAMESPACE} \
                          set image deployment/easyshop \
                          easyshop=${ECR_IMAGE}

                        echo "===== ROLLOUT STATUS ====="

                        kubectl -n ${K8S_NAMESPACE} \
                          rollout status deployment/easyshop \
                          --timeout=180s
                    '''
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'aws-ecr']
                ]) {
                    sh '''
                        export KUBECONFIG=${KUBECONFIG}

                        echo "===== DEPLOYMENT ====="
                        kubectl get deployment easyshop \
                          -n ${K8S_NAMESPACE}

                        echo "===== PODS ====="
                        kubectl get pods \
                          -n ${K8S_NAMESPACE} \
                          -o wide

                        echo "===== SERVICE ====="
                        kubectl get svc \
                          -n ${K8S_NAMESPACE}

                        echo "===== HPA ====="
                        kubectl get hpa \
                          -n ${K8S_NAMESPACE}

                        echo "===== INGRESS ====="
                        kubectl get ingress \
                          -n ${K8S_NAMESPACE}
                    '''
                }
            }
        }

        stage('Verify HPA Metrics') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'aws-ecr']
                ]) {
                    sh '''
                        export KUBECONFIG=${KUBECONFIG}

                        echo "===== HPA DETAILS ====="
                        kubectl describe hpa easyshop \
                          -n ${K8S_NAMESPACE}

                        echo "===== POD METRICS ====="
                        kubectl top pods \
                          -n ${K8S_NAMESPACE} || true
                    '''
                }
            }
        }
    }

    post {

        success {
            emailext(
                to: 'developerprasad479@gmail.com',
                subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
Hello Prasad,

EasyShop DevSecOps CI/CD pipeline completed successfully.

Job:
${env.JOB_NAME}

Build:
#${env.BUILD_NUMBER}

Status:
SUCCESS

Docker Image:
${env.ECR_IMAGE}

EKS Cluster:
${env.EKS_CLUSTER}

Namespace:
${env.K8S_NAMESPACE}

Deployment:
easyshop

HPA:
1-10 replicas

The application was successfully deployed to Amazon EKS.

Jenkins:
${env.BUILD_URL}

Regards,
Jenkins CI/CD
"""
            )
        }

        failure {
            emailext(
                to: 'developerprasad479@gmail.com',
                subject: "FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
Hello Prasad,

EasyShop DevSecOps CI/CD pipeline FAILED.

Job:
${env.JOB_NAME}

Build:
#${env.BUILD_NUMBER}

Status:
FAILED

EKS Cluster:
${env.EKS_CLUSTER}

Please check the Jenkins console output.

Jenkins:
${env.BUILD_URL}

Regards,
Jenkins CI/CD
"""
            )
        }

        always {
            echo 'Pipeline execution finished.'
        }
    }
}
