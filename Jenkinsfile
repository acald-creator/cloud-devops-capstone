pipeline {
    agent any
    stages {
        stage('Connect local Kubernetes cluster to AWS EKS') {
            steps {
                withAWS(region:'us-east-1', credentials:'aws-static') {
                    sh '''
                        aws eks --region us-east-1 update-kubeconfig --name hilarious-monster-1646618989
                        '''
                }
            }
        } 
        stage('Lint HTML') {
            steps {
                sh 'tidy -q -e *.html'
            }
        }
        stage('Build Docker image Blue') {
            steps {
                withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]){
                    sh '''
                        docker build -t pyrrhus/udacity-capstone:blue --build-arg IMAGE_ID="nginx:1.20" .
                        '''
            }
        }
    }
        stage('Build Docker image Green') {
            steps {
                withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]){
                    sh '''
                        docker build -t pyrrhus/udacity-capstone:green --build-arg IMAGE_ID="nginx:1.21" .
                        '''
            }
        }
    }
    stage('Push image to DockerHub') {
        steps {
                withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]){
                    sh '''
                        docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
                        docker push pyrrhus/udacity-capstone:blue
                        docker push pyrrhus/udacity-capstone:green
                    '''
                }
            }
        }
    stage('Set current kubectl context') {
            steps {
                withAWS(region:'us-east-1', credentials:'aws-static') {
                    sh '''
                        kubectl config use-context arn:aws:eks:us-east-1:259445498767:cluster/hilarious-monster-1646618989
                    '''
                }
            }
        }
        stage('Deploy blue containers for blue environment') {
            steps {
                withAWS(region:'us-east-1', credentials:'aws-static') {
        					sh '''
        						sed -e 's/{{TARGET_ROLE}}/blue/g' -e 's/{{IMAGE_VERSION}}/blue/' deployment.yaml | kubectl apply -f -
        					'''
        				}
        			}
        		}
                stage('Deploy blue containers for green environment') {
                    steps {
                        withAWS(region:'us-east-1', credentials:'aws-static') {
                					sh '''
                						sed -e 's/{{TARGET_ROLE}}/green/g' -e 's/{{IMAGE_VERSION}}/blue/' deployment.yaml | kubectl apply -f -
                					'''
                				}
                			}
                		}

         stage('Create service for cluster and redirect to blue') {
         			steps {
         				withAWS(region:'us-east-1', credentials:'aws-static') {
         					sh '''
         						sed  's/{{TARGET_ROLE}}/blue/g' service.yaml | kubectl apply -f -
         					'''
         				}
         			}
         		}

         stage('Deploy green containers for green environment') {
                         			steps {
                         				withAWS(region:'us-east-1', credentials:'aws-static') {
                         					sh '''
                         						sed -e 's/{{TARGET_ROLE}}/green/g' -e 's/{{IMAGE_VERSION}}/green/' deployment.yaml | kubectl apply -f -
                         					'''
                         				}
                         			}
                         		}

        stage('Create service to test green') {
                 			steps {
                 				withAWS(region:'us-east-1', credentials:'aws-static') {
                 					sh '''
                 						sed  's/{{TARGET_ROLE}}/green/g' service-test.yaml | kubectl apply -f -
                 					'''
                 				}
                 			}
                 		}



        stage('Wait for user approval') {
             steps {
                      input "Are you ready to redirect traffic to green environment?"
              }
         }

        stage('Update service for cluster and redirect to green') {
                 			steps {
                 				withAWS(region:'us-east-1', credentials:'aws-static') {
                 					sh '''
                 						sed  's/{{TARGET_ROLE}}/green/g' service.yaml | kubectl apply -f -
                 					'''
                 				}
                 			}
                 		}
                    }
                }