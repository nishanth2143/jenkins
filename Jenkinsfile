node('docker') {

    stage 'Checkout'
        checkout scm

    stage 'Build & UnitTest'
    sh "docker build -t cnxearninganddeductions:B${BUILD_NUMBER} -f Cnx.payroll/Dockerfile ."
    sh "docker build -t my-registry:5000/convergysapp:B${BUILD_NUMBER} -f Cnx.payroll/Dockerfile ."	
    sh "docker build -t cnxearninganddeductions:test-B${BUILD_NUMBER} -f Cnx.payroll/Dockerfile.Integration ."
    
    stage 'Publish UT Reports'
        
        containerID = sh (
            script: "docker run -d cnxearninganddeductions:B${BUILD_NUMBER}", 
        returnStdout: true
        ).trim()
        echo "Container ID is ==> ${containerID}"
        sh "docker cp ${containerID}:/TestResults/test_results.xml test_results.xml"
        sh "docker stop ${containerID}"
        sh "docker rm ${containerID}"
        step([$class: 'MSTestPublisher', failOnError: false, testResultsFile: 'test_results.xml'])    
      
    stage 'Integration Test'
         // sh "docker-compose -f docker-compose.integration.yml up"
         sh "docker-compose -f Cnx.payroll/docker-compose.integration.yml up --force-recreate --abort-on-container-exit"
         sh "docker-compose -f Cnx.payroll/docker-compose.integration.yml down -v"

    stage("Push Image")
        sh "docker push my-registry:5000/cnxearninganddeductions:B${BUILD_NUMBER}"

	// stage("Deploy to k8s")
    //     sh "sed -i 's/BUILD_NUMBER/B${BUILD_NUMBER}/g' mydeploy.yaml"
	// 	sh "kubectl apply -f mydeploy.yaml"
	// 	sh "kubectl apply -f myservice.yaml"
    
}