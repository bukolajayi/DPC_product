node{
     
    stage('Git Clone'){
        git branch: 'main', credentialsId: 'GITHUB_CRED', url: 'https://github.com/bukolajayi/DPC_product.git'
    }
   
    stage(" Maven Clean Package"){
      def mavenHome =  tool name: "maven3.8.5", type: "maven"
      def mavenCMD = "${mavenHome}/bin/mvn"
      sh "${mavenCMD} clean package"
      
    } 
    
    /**stage('Code Quality'){
        sh "docker rm -f sonar"
        sh "docker run --name sonar -d -p 9000:9000 sonarqube"
        sh "docker ps"
        def mavenHome =  tool name: "maven3.8.5", type: "maven"
        def mavenCMD = "${mavenHome}/bin/mvn"
        sh "${mavenCMD} sonar:sonar"
    }
    **/
    stage('Manual Approval'){
        sh "echo Approval for DPC"
        timeout(time:5, unit:'DAYS'){
            input message:'Approval for Production'
        }
    }
  
    stage('Build Docker Image'){
        sh 'docker build -t bukolajayi/dpc-product .'
    }
    
    stage('Push Docker Image'){
        withCredentials([string(credentialsId: 'DOCKER_HUB_CRED2', variable: 'DOCKER_HUB_CRED2')]) {
          sh "docker login -u bukolajayi -p ${DOCKER_HUB_CRED2}"
        }
        sh 'docker push bukolajayi/dpc-product'
     }
     
     stage("Deploy To Kuberates Cluster"){
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', serverUrl: '') {
                sh '/var/lib/jenkins/bin/kubectl apply -f springBootMongo.yml'
                sh '/var/lib/jenkins/bin/kubectl get all -A'
                sh '/var/lib/jenkins/bin/kubectl get no'
            }
                
     }
     }
