pipeline {   
    agent any   
    environment {       
        registry = "itsdenty/k8scicd"       
        GOCACHE = "/tmp"   
    }   
    stages {       
        stage('Build') {           
            agent {               
                docker {                   
                    image 'golang'               
                }           
            }           
            steps {               
                // Create our project directory.               
                sh 'cd ${GOPATH}/src'               
                sh 'mkdir -p ${GOPATH}/src/hello-world' // Copy all files in our Jenkins workspace to our project directory.                              
                sh 'cp -r ${WORKSPACE}/* ${GOPATH}/src/hello-world'               // Build the app.               
                sh 'go build'                         }
            }       
            stage('Test') {           
                agent {               
                    docker {                   
                        image 'golang'               
                    }           
                }           
                steps {  // Create our project directory.               
                sh 'cd ${GOPATH}/src'               
                sh 'mkdir -p ${GOPATH}/src/hello-world'  // Copy all files in our Jenkins workspace to our project directory.                              
                sh 'cp -r ${WORKSPACE}/* ${GOPATH}/src/hello-world' // Remove cached test results.               
                sh 'go clean -cache'               // Run Unit Tests.               
                sh 'go test ./*_test.go -v -short'                     
            }       
        }       
        stage('Publish') {           
            environment {               
                registryCredential = 'dockerhub'           
            }           
            steps{               
                script {                   
                    def appimage = docker.build registry + ":$BUILD_NUMBER"                   
                    docker.withRegistry( '', registryCredential ) {                       
                        appimage.push()                       
                        appimage.push('latest')                   
                    }               
                }           
            }       
        }       
        stage ('Deploy') {   
            // environment {               
            //     BECOME_PASSWORD = credentials('sudopass')
            // }          
            steps {               
                script{                   
                    def image_id = registry + ":$BUILD_NUMBER"                   
                    sh "ansible-playbook playbook.yml --extra-vars \"image_id=${image_id} ansible_become_pass=4d3d4m0l4\""            
                }           
            }       
        }   
    }
}
