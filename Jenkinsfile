node{
	
	currentBuild.displayName = "1.0.${BUILD_NUMBER}"
	def GIT_COMMIT
  stage ('cloning the repository'){
      def result = git 'https://github.com/jitendra-git123/Altoro-j.git'  
     GIT_COMMIT = result.GIT_COMMIT
  }
	
  stage('Gradle Build') {
     //def gradleHome = tool name : 'mygradle', type 'gradle',
        //sh "${gradleHome}/bin/gradle clean build"
        def path = tool name: 'gradle-4.7', type: 'gradle'
        bat "${path}/bin/gradle build"
   }
   
   stage('SonarQube analysis') {
    def path = tool name: 'gradle-4.7', type: 'gradle'
    withSonarQubeEnv('sonar-server') {
       bat "${path}/bin/gradle --info -Dsonar.host.url=http://localhost:9000 sonarqube"
    }
	
   } 	
 
   stage ("Appscan"){
     //  appscan application: '84963f4f-0cf4-4262-9afe-3bd7c0ec3942', credentials: 'Credential for ASOC', failBuild: true, failureConditions: [failure_condition(failureType: 'high', threshold: 20)], name: '84963f4f-0cf4-4262-9afe-3bd7c0ec39421562', scanner: static_analyzer(hasOptions: false, target: 'D:/Installables/Jenkins/workspace/Velocity/AltoroJ/build/libs/'), type: 'Static Analyzer'
  	build job: 'Velocity/asoc123', wait: false, parameters: [
  	string(name: 'COMMITID', value: GIT_COMMIT),
	string(name: 'parentBuildNumber', value: BUILD_NUMBER)
		
	]
   }
	
   stage('Publish Artificats to UCD'){
	   step([$class: 'UCDeployPublisher',
	        siteName: 'UCD_Local',
	        component: [
	            $class: 'com.urbancode.jenkins.plugins.ucdeploy.VersionHelper$VersionBlock',
	            componentName: 'AltoroComponent',
	            createComponent: [
	                $class: 'com.urbancode.jenkins.plugins.ucdeploy.ComponentHelper$CreateComponentBlock',
	                componentTemplate: '',
	                componentApplication: 'Altoro'
	            ],
	            delivery: [
	                $class: 'com.urbancode.jenkins.plugins.ucdeploy.DeliveryHelper$Push',
	                pushVersion: '1.0.${BUILD_NUMBER}',
	                //baseDir: '/var/jenkins_home/workspace/JPetStore/target',
			 baseDir: 'D:/Installables/Jenkins/workspace/Velocity/AltoroJ/build/libs/',
	                fileIncludePatterns: '*.war',
	                fileExcludePatterns: '',
	               // pushProperties: 'jenkins.server=Jenkins-app\njenkins.reviewed=false',
	                pushDescription: 'Pushed from Jenkins'
	            ]
	        ]
	    ])
	  
		//sh 'env > env.txt'
		//readFile('env.txt').split("\r?\n").each {
		//println it
		//}
	echo "(*******)"
	  echo "Demo1234 ${AltoroComponent_VersionId}"
	  def newComponentVersionId = "${AltoroComponent_VersionId}"
	  echo "git commit ${GIT_COMMIT}"
	  //step($class: 'UploadBuild', tenantId: "5ade13625558f2c6688d15ce", revision: "${GIT_COMMIT}", appName: "Altoro", requestor: "admin", id: "${newComponentVersionId}" )
  step($class: 'UploadBuild', 
       tenantId: "5ade13625558f2c6688d15ce", 
       revision: "${GIT_COMMIT}", 
       appName: "AltoroJ", 
       requestor: "admin", 
       id: "${newComponentVersionId}", 
       versionName: "1.0.${BUILD_NUMBER}"
      )
     
	echo "Demo123 ${newComponentVersionId}"
	sleep 25
	  step([$class: 'UCDeployPublisher',
		deploy: [ createSnapshot: [deployWithSnapshot: true, 
			 snapshotName: "1.0.${BUILD_NUMBER}"],
			 deployApp: 'Altoro', 
			 deployDesc: 'Requested from Jenkins', 
			 deployEnv: 'Altoro_Dev', 
			 deployOnlyChanged: false, 
			 deployProc: 'Deploy-Altoro', 
			 deployReqProps: '', 
			 deployVersions: "AltoroComponent:1.0.${BUILD_NUMBER}"], 
		siteName: 'UCD_Local'])

		  
 }
}
