pipeline {
	agent any
	stages{
		stage('Build') {
			steps {
				sh 'mvn package -DskipTests'
						
			}		
		}
		stage('BUILD - Docker Images') 
		{
			steps 
			{
				sh 'docker build -t vishnusaisankeerth/br:apiimg .'
				sh 'docker build -t vishnusaisankeerth/br:sqlimg -f mysql.Dockerfile .'	
			}
		} 

		
		stage('TEST')
		{
			parallel
			{
				stage("TEST - Running Test") 
				{
					steps 
					{	
						script 
						{
                                                      try
							{
							    sh 'docker-compose up -d'
							    sh 'sleep 60'
							    sh 'docker exec project_container mvn -f /usr/app test'
							    currentBuild.result = 'SUCCESS'
							    sh 'docker-compose stop'
							}
							catch(Exception ex) 
							{
								currentBuild.result = 'ABORTED'
								sh 'docker-compose stop'
								error('Test Cases Failed')

							}
							
						}

					}
				}
			}
		}

		stage('PUBLISH to DockerHub') 
		{
		    steps 
		    {
	        	withDockerRegistry([ credentialsId: "dockerHub", url: "" ]) 
	        	{
				
	        		sh 'docker push vishnusaisankeerth/br:apiimg'
	        		sh 'docker push vishnusaisankeerth/br:sqlimg'
				sh 'docker stop brsql_container'
				sh 'docker rm brsql_container'
				sh 'docker stop brproject_container'
				sh 'docker rm brproject_container'
	      		}
		    }
		}
	}
	post
	{
		always
		{
			sh 'echo "Pipeline Finished"'
		}
		success
		{
			sh 'curl --location --request POST "http://localhost:4440/api/21/job/80d86921-78ce-4e14-b6d1-76c3f1f106a8/run" \
 			--header "Accept: application/json" \
 			--header "X-Rundeck-Auth-Token: aFbPNoYDgrq7uIu0YB6A7C1buiUdZ9jh" \
 			--header "Content-Type: application/json" \
 			--data ""'
		}
  	}
	
}
