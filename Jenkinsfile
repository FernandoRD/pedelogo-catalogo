pipeline{
    agent {Antares}

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
            agent{
                kubernetes{
                    cloud 'microk8s'
                }
            }
            environment{
                tag_version = "${env.BUILD_ID}"
            }
            steps{
                script{
                    sh 'sed -i "s/{{tage/$tag_version/g}}" ./k8s/api/deployment.yaml'
                    sh 'cat ./k8s/api/deployment.yaml'
                    kubernetesDeploy(configs: '**/k8s/**', kubeconfigId: 'microk8s')
                }
            }
        }
    }
}