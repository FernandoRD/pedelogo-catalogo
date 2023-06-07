pipeline{
    agent any

    stages{
        stage('Checkout Source'){
            steps{
                git url:'https://github.com/FernandoRD/pedelogo-catalogo.git', branch:'main'
            }        
        }
        stage('Build Image'){
            steps{
                script{
                    dockerapp = docker.build("fernandord/pedelogo-catalogo:${env.BUILD_ID}", '-f ./src/PedeLogo.Catalogo.Api/Dockerfile .')
                }
            }
        
        }
        stage('Push Image'){
            steps{
                script{
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub'){
                        dockerapp.push('latest')
                        dockerapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }
        stage('Deploy Kubernetes'){
            steps{
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'microk8s', namespace: '', restrictKubeConfigAccess: false, serverUrl: '192.168.0.181'){
                sh 'kubectl apply -f k8s/*'
                }
            }
        }
    }
}