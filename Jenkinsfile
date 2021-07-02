pipeline {
    agent {
        label 'nginx-server'
    }
     environment {
        IMAGE = 'my_nginx'
        VERSION    = 'latest'
    }
    stages {

        stage('scm') {
            steps {
                git branch: 'main', url: 'https://github.com/kush95300/test-nginx.git'
                stash includes: '*' , name: 'nginx-repo'
            }
        }
        stage('build') {
            steps {
                unstash 'nginx-repo'
                sh '''
                timestamp=$(date "+%y%m%d%H%M%S")
                old="${IMAGE}:changed$timestamp"
                new="${IMAGE}:${VERSION}"
                echo $timestamp $old $new
                if [[ ${VERSION} == 'latest' ]]
                then 
                if sudo docker tag $new $old
                then
                sudo docker rmi $new
                fi
                sudo docker build -t $new .
                else
                new="${IMAGE}:${VERSION}"
                sudo docker build -t $new .
                fi
                
                '''
            }
        }
        stage('deploy') {
            steps {
                sh '''
                if sudo docker ps -a | grep nginx_production_webapp
                then
                if sudo docker ps | grep nginx_production_webapp
                then    
                sudo docker stop nginx_production_webapp
                fi
                sudo docker rm nginx_production_webapp
                fi
                image="${IMAGE}:${VERSION}"
            
                sudo docker run -dit -p 80:80 --name nginx_production_webapp $image
                '''
                
            }
        }
    }
}
