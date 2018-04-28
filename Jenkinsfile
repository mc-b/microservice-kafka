pipeline {
    agent none
    stages {
        stage('Build') {
		    agent {
		        docker {
		            image 'maven:3-alpine'
		            args '-v /root/.m2:/root/.m2'
			    }
		    }         
            steps {
                sh 'cd microservice-kafka/ && mvn -B -DskipTests clean package'
                archiveArtifacts 'microservice-kafka/**/*.jar'
                stash includes: 'microservice-kafka/**/*.jar', name: 'jar'
            }
        }
        stage('Build Images') { 
        	agent any
            steps {
            		unstash 'jar'
				    sh 'docker build -t misegr/mskafka_apache docker/apache'
				    sh 'docker build -t misegr/mskafka_postgres docker/postgres'
				    sh 'docker build -t misegr/mskafka_order microservice-kafka/microservice-kafka-order'
				    sh 'docker build -t misegr/mskafka_shipping microservice-kafka/microservice-kafka-shipping'
				    sh 'docker build -t misegr/mskafka_invoicing microservice-kafka/microservice-kafka-invoicing'         		
            }
        }
        stage('Test') {
		    agent {
		        docker {
		            image 'maven:3-alpine'
		            args '-v /root/.m2:/root/.m2'
			    }
		    }           
            steps {
                sh 'cd microservice-kafka/ && mvn test'
            }
            post {
                always {
                  junit 'microservice-kafka/**/surefire-reports/*.xml'
                }
            }
        }        
    }
}
