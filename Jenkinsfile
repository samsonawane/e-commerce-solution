def workspace, props='', commit_username, commit_Email, appDeployProcess, imageName='', envMessage='', pomfile='';
def module='', apiversion, lstChangedModules='', modulesToBeDeployed='' , branchName='development' ;
node  {
timestamps {
    stage('Checkout Code')
    {
        try
        {
            checkout scm;
            props = readProperties  file: """deploy.properties""";
			pomfile = readMavenPom file: '';
			workspace = pwd ();
			//branchName=sh(returnStdout: true, script: 'git symbolic-ref --short HEAD').trim();
			commit_username=sh(returnStdout: true, script: '''username=$(git log -1 --pretty=%ae) 
																echo ${username%@*} ''').trim();
			commit_Email=sh(returnStdout: true, script: '''Email=$(git log -1 --pretty=%ae) 
																echo $Email''').trim();
			module = pomfile.modules.join(",");
			module = module.tokenize(",")
			apiversion = pomfile.version
			if ("""${modulesToBeDeployed}""" != "")
			{
				lstChangedModules = """${modulesToBeDeployed}""".trim().toLowerCase()
				lstChangedModules = lstChangedModules.tokenize(",")
			}
			else
			{
				lstChangedModules = getModule().tokenize(",")
			}
			
        	echo """lstChangedModules - ${lstChangedModules}"""
			echo commit_username
			echo commit_Email
			//print e-commerce-solution modules
			for(j=0; j<module.size();j++)
			{
				i=j+1
				echo """Module ${i} - ${module[j]}"""
			}
			/*switch(props['api.deploy.type'].trim().toUpperCase()) 
			{ 
				case "CF":
					appDeployProcess="Deploy_cf"
					break;
				case "CN":
					appDeployProcess="Deploy_container"
					break;
				default:
				echo "java api can deployed on 'CF' or 'CN', \n Please provide valid deployment type."
					error("")
					break;
			}*/
        }
    	catch (e) {
    		currentBuild.result='FAILURE'
    		//logJIRATicket(currentBuild.result, "At Stage Checkout Code", props['JIRAprojectid'], props['JIRAissuetype'], commit_Email, props['JIRAissuereporter'])
    		notifyBuild(currentBuild.result, "At Stage Checkout Code", "", commit_Email)
    		throw e
    	}
		catch (error) {
				currentBuild.result='FAILURE'
				//logJIRATicket(currentBuild.result, "At Stage Checkout Code", props['build.JIRAprojectid'], props['build.JIRAissuetype'], commit_Email, props['build.JIRAissuereporter'])
				notifyBuild(currentBuild.result, "At Stage Checkout Code", "", commit_Email)
				throw error
			}
    }
	stage ('Check Environment')
    {  
    	try
		{
			echo "check environment"
			/* def zipkinstatus=sh(returnStdout: true, script: """curl -s ${props['ZipkinURL']}/health | jq '.status' | tr -d '"' """).trim();
			echo """Zipkin status is - ${zipkinstatus}"""
			def kibanastatus=sh(returnStdout: true, script: """curl -s ${props['KibanaURL']}/api/status | jq '.status.overall.state' | tr -d '"' """).trim();
			echo """Kibana status is - ${kibanastatus}"""
			def eurekastatus=sh(returnStdout: true, script: """curl -s ${props['EurekaURL']}/health | jq '.status' | tr -d '"' """).trim();
			echo """Eureka status is - ${eurekastatus}"""
			def prometheusstatus=sh(returnStdout: true, script: """curl -s -g ${props['PrometheusURL']}/api/v1/series?match[]=up{job=%22prometheus%22}| jq '.status' | tr -d '"' """).trim();
			echo """Prometheus status is - ${prometheusstatus}"""
			def grafanastatus=sh(returnStdout: true, script: """curl -s ${props['GrafanaURL']}/api/health  --write-out " %{http_code}" --output NULL  | tr -d '"' """).trim();
			echo """Grafana status is - ${grafanastatus}"""
			if(zipkinstatus != "UP")
			{
				envMessage = """\n Zipkin server is not running (Status:${zipkinstatus})"""
			}
			if(kibanastatus == "red")
			{
			    	envMessage = envMessage + """\n Kibana server is not running  (Status:${kibanastatus}) """
			}
			if(eurekastatus != "UP")
			{
			    	envMessage = envMessage + """\n Eureka server is not running  (Status:${eurekastatus})"""
			}
			if(prometheusstatus != "success")
			{
			    	envMessage = envMessage + """\n Prometheus server is down (Status:${prometheusstatus})"""
			}
			if(grafanastatus != "200")
			{
			    	envMessage = envMessage + """\n Grafana dashboard is not running (Status:${grafanastatus})"""
			}
			if (envMessage!="")
			{
			    	error ("""${envMessage} \n """)
			} */
		}
	 catch (e) {
    		currentBuild.result='FAILURE'
    		//logJIRATicket(currentBuild.result,  "At Stage Check Environment", props['JIRAprojectid'], props['JIRAissuetype'], commit_Email, props['JIRAissuereporter'])
    		notifyBuild(currentBuild.result, "At Stage Check Environment", "", commit_Email)
    		throw e
    	}
    }
	 stage ('Build Module')
    { 
    	try
		{
			parallel(
				build1: {if ((lstChangedModules.find{ it == "customer-info"}) || (lstChangedModules.find{ it == "all"})) {buildModule("customer-info", branchName, props['docker.registry'])} else { echo "no change"}},
				build2: {if ((lstChangedModules.find{ it == "inventory"}) || (lstChangedModules.find{ it == "all"})) {buildModule("inventory", branchName, props['docker.registry'])} else { echo "no change"}},
				build3: {if ((lstChangedModules.find{ it == "order-info"}) || (lstChangedModules.find{ it == "all"})) {buildModule("order-info", branchName, props['docker.registry'])} else { echo "no change"}},
				build4: {if ((lstChangedModules.find{ it == "product-catalog"}) || (lstChangedModules.find{ it == "all"})) {buildModule("product-catalog", branchName, props['docker.registry'])} else { echo "no change"}},
				build5: {if ((lstChangedModules.find{ it == "recommendation"}) || (lstChangedModules.find{ it == "all"})) {buildModule("recommendation", branchName, props['docker.registry'])} else { echo "no change"}},
				build6: {if ((lstChangedModules.find{ it == "return-and-refunds"}) || (lstChangedModules.find{ it == "all"})) {buildModule("return-and-refunds", branchName, props['docker.registry'])} else { echo "no change"}},
				build7: {if ((lstChangedModules.find{ it == "review"}) || (lstChangedModules.find{ it == "all"})) {buildModule("review", branchName, props['docker.registry'])} else { echo "no change"}},
				build8: {if ((lstChangedModules.find{ it == "shipping"}) || (lstChangedModules.find{ it == "all"})) {buildModule("shipping", branchName, props['docker.registry'])} else { echo "no change"}},
				build9: {if ((lstChangedModules.find{ it == "shopping-cart"}) || (lstChangedModules.find{ it == "all"})) {buildModule("shopping-cart", branchName, props['docker.registry'])} else { echo "no change"}},
				build10: {if ((lstChangedModules.find{ it == "wishlist"}) || (lstChangedModules.find{ it == "all"})) {buildModule("wishlist", branchName, props['docker.registry'])} else { echo "no change"}},
				build11: {if ((lstChangedModules.find{ it == "payment"}) || (lstChangedModules.find{ it == "all"})) {buildModule("payment", branchName, props['docker.registry'])} else { echo "no change"}},
			)
		}
	 catch (e) {
    		currentBuild.result='FAILURE'
    		//logJIRATicket(currentBuild.result,  "At Stage Build	", props['JIRAprojectid'], props['JIRAissuetype'], commit_Email, props['JIRAissuereporter'])
    		notifyBuild(currentBuild.result, "At Stage Build", "", commit_Email)
    		throw e
    	}
    }
	stage ('Deploy to Kubernetes Cluster')
    { 
        try {
			def addSubChart='',isdeployment=false;
			// sh """mkdir helmchart_deploy helmchart_deploy/e-commerce-solution
			// 		cd helmchart/e-commerce-solution
			// 		cp -vr values.yaml \
			// 		templates/ \
			// 		Chart.yaml ../../helmchart_deploy/e-commerce-solution/"""
			for(j=0; j<module.size();j++)
			{
				i=j+1
				if ((lstChangedModules.find{ it == module[j]}) || (lstChangedModules.find{ it == "all"}))
				{
					isdeployment=true;
					echo """Module ${i} - ${module[j]}"""
					imageName="""${props['docker.registry']}/${module[j]}:${apiversion}"""
					sh """imageName='  Image: "${imageName}"'
					findstr='  Image: "${module[j]}ImageName"'
					sed -i "s|\$findstr|\$imageName|g" helmchart/e-commerce-solution/values.yaml
					"""
					//cp -vr helmchart/e-commerce-solution/charts/${module[j].replaceAll("[^a-zA-Z0-9 ]+","")}/**/* helmchart_deploy/e-commerce-solution/charts/
					if (addSubChart == '')
					{
						addSubChart = "helmchart/e-commerce-solution/charts/${module[j].replaceAll("[^a-zA-Z0-9 ]+","")}/**/*"
					}
					else
					{
						addSubChart = addSubChart + "\nhelmchart/e-commerce-solution/charts/${module[j].replaceAll("[^a-zA-Z0-9 ]+","")}/**/*"
					}
				}
			}
			if (isdeployment)
			{
				sh """sed -i "s|kubernetesnamespace|${props['kubernetesnamespace']}|g" helmchart/e-commerce-solution/values.yaml"""
		// 		step([$class: 'UCDeployPublisher',
		// 			siteName: 'local',
		// 			component: [
		// 				$class: 'com.urbancode.jenkins.plugins.ucdeploy.VersionHelper$VersionBlock',
		// 				componentName: """${JOB_NAME}""", 
		// 				delivery: [
		// 					$class: 'com.urbancode.jenkins.plugins.ucdeploy.DeliveryHelper$Push',
		// 					pushVersion: """${JOB_NAME}_${BUILD_NUMBER}_${BUILD_TIMESTAMP}""",
		// 					baseDir: """${workspace}""",
		// 					fileIncludePatterns: """deploy.properties
		// 											helmchart/${JOB_NAME}/templates/**/*
		// 											helmchart/${JOB_NAME}/Chart.yaml
		// 											helmchart/${JOB_NAME}/values.yaml
		// 											${addSubChart}""",
		// 					fileExcludePatterns: '',
		// 					pushDescription: """Build for ${JOB_NAME} ON  :${BUILD_TIMESTAMP}""",
		// 					pushIncremental: false
		// 					]
		// 			],
		// 			deploy: [
		// 				$class: 'com.urbancode.jenkins.plugins.ucdeploy.DeployHelper$DeployBlock',
		// 				deployApp: """${JOB_NAME}""",
		// 				deployEnv: """${props['deploy.defaultEnvironment']}""",
		// 				deployProc: """${appDeployProcess}""",
		// 				deployVersions: """${JOB_NAME}:${JOB_NAME}_${BUILD_NUMBER}_${BUILD_TIMESTAMP}""",
		// 				deployOnlyChanged: true
		// 			]
		// 		])
			}				
        }
    	catch (e) {
    		currentBuild.result='FAILURE'
    		//logJIRATicket(currentBuild.result, "At Stage Deploy", props['JIRAprojectid'], props['JIRAissuetype'], commit_Email, props['JIRAissuereporter'])
    		notifyBuild(currentBuild.result, "At Stage Deploy", "", commit_Email)
    		throw e
    	}
    }
	stage ('Validate Microservice Deployment')
    { 
        try {
			echo "validate environment"
				/* sleep 120
				//def chkmicroservice=sh(returnStdout: true, script: """curl -s http://${props['environment.URL']}:${props['app.port']}/health | jq '.status' | tr -d '"' """).trim();
				
				def chkEurekaConnection=sh(returnStdout: true, script: """curl -s http://${props['environment.URL']}:${props['app.port']}/health | jq '.discoveryComposite.eureka.status' | tr -d '"' """).trim();
				//echo """status is - ${chkEurekaConnection}"""
				def chkDBConnection=sh(returnStdout: true, script: """curl -s http://${props['environment.URL']}:${props['app.port']}/health | jq '.${props['datasource']}.status' | tr -d '"' """).trim();
				//echo """status is - ${chkDBConnection}"""
				def chkConfigServerConnection=sh(returnStdout: true, script: """curl -s http://${props['environment.URL']}:${props['app.port']}/health | jq '.configServer.status' | tr -d '"' """).trim();
				//echo """status is - ${chkConfigServerConnection}"""
				def chkHystrixConnection=sh(returnStdout: true, script: """curl -s http://${props['environment.URL']}:${props['app.port']}/health | jq '.hystrix.status' | tr -d '"' """).trim();
				//echo """status is - ${chkHystrixConnection}"""
				def chkZipkinConnection=sh(returnStdout: true, script: """curl ${props['ZipkinURL']}/api/v1/traces?serviceName=${props['deploy.app']} | tr -d '[]' """).trim();
				//echo """status is - ${chkZipkinConnection}"""
				def chkPrometheusConnection=sh(returnStdout: true, script: """curl -sg ${props['PrometheusURL']}/api/v1/series?match[]={instance=%22${props['environment.URL']}:${props['app.port']}%22} | jq '.data[]' > temp; if [ -s temp ]; then echo "true"; else echo "false"; fi""").trim();
				//echo """status is - ${chkPrometheusConnection}"""

				def chkDeployment=''; */
				/* if(chkmicroservice != "UP")
				{
					chkDeployment = chkDeployment + """\n Microservice - ${JOB_NAME} connection failed (Status:${chkmicroservice})"""
				} */
				/*if(chkEurekaConnection != "UP")
				{
					chkDeployment = """\n Eureka connection failed (Status:${chkEurekaConnection})"""
				}
				if(chkDBConnection != "UP")
				{
					chkDeployment = chkDeployment + """\n Database connection failed (Status:${chkDBConnection}) """
				}
				if(chkConfigServerConnection != "UP")
				{
					chkDeployment = chkDeployment + """\n connection failed (Status:${chkConfigServerConnection})"""
				}
				if(chkHystrixConnection != "UP")
				{
					chkDeployment = chkDeployment + """\n Hystrix connection failed (Status:${chkHystrixConnection})"""
				}
				if(chkZipkinConnection == "")
				{
					chkDeployment = chkDeployment + """\n Zipkin connection failed (Status:${chkZipkinConnection})"""
				}
				if(chkPrometheusConnection == "false")
				{
					chkDeployment = chkDeployment + """\n Prometheus connection failed (Status:${chkPrometheusConnection})"""
				}

				if (chkDeployment != "")
				{
					error ("""\n Warning:\n Microservice deployment is unstable ${chkDeployment} \n """)
				} */
			}
			catch (e) {
				currentBuild.result='FAILURE'
				//logJIRATicket(currentBuild.result, "At Stage Validate Microservice Deployment", props['JIRAprojectid'], props['JIRAissuetype'], commit_Email, props['JIRAissuereporter'])
				notifyBuild(currentBuild.result, "At Stage Validate Microservice Deployment", "", commit_Email)
				throw e
			}
			catch (error) {
				currentBuild.result='UNSTABLE'
				//logJIRATicket(currentBuild.result, "At Stage Validate Microservice Deployment", props['JIRAprojectid'], props['JIRAissuetype'], commit_Email, props['JIRAissuereporter'])
				notifyBuild(currentBuild.result, "At Stage Validate Microservice Deployment", "", commit_Email)
				echo """${error.getMessage()}"""
				//throw e
			}
    }
    stage ('Log JIRA Ticket for Code Promotion')
    {
        try {
			echo "log JIRA for Code promotion"
            //logJIRATicket('SUCCESS', "At Stage Log JIRA Ticket", props['JIRAprojectid'], props['JIRAissuetype'], commit_Email, props['JIRAissuereporter'])
    	}
    	catch (e) {
    		currentBuild.result='FAILURE'
    		//logJIRATicket(currentBuild.result, "At Stage Log JIRA Ticket", props['JIRAprojectid'], props['JIRAissuetype'], commit_Email, props['JIRAissuereporter'])
    		notifyBuild(currentBuild.result ,"At Stage Log JIRA Ticket", """no JIRA ticket logged.""", commit_Email)
			throw e
    	}
    }
   notifyBuild(currentBuild.result, "", """\n Build successfull, no JIRA ticket logged. """, commit_Email)
}   
}
def notifyBuild(String buildStatus, String buildFAILUREAt,String bodyDetails,String commit_username)
{
	/*buildStatus = buildStatus ?: 'SUCCESS'
	if (buildStatus == 'FAILURE' )
	{
		msgcolor='#e01563'
		}
		else
		{
		msgcolor='#3eb991'
		}
	detials="""${JOB_NAME}: ${buildStatus} ${buildFAILUREAt}		Initiated by: ${commit_username}  \n\n Please Check build logs at :-${BUILD_URL}"""
			slackSend color: msgcolor, baseUrl: 'https://bcg.slack.com/services/hooks/jenkins-ci/', channel: '#dbs', message: detials, token: 'TQ4I97X8ouGnWWTGbkyaWwWj'
	//slackSend color: msgcolor, message: detials, channel: 'dbs' */
}
def logJIRATicket(String buildStatus, String buildFailedAt, String projectid, String issuetype, String assignTo, String issueReporter) {
	/*buildStatus = buildStatus ?: 'SUCCESS'
	if (buildStatus == 'FAILURE' ){
	String Title="""${buildStatus} ${buildFailedAt} OF ${JOB_NAME}[${BUILD_NUMBER}]"""
	withEnv(['JIRA_SITE=JIRA-DBS']) {
		// Look at IssueInput class for more information.
	def Issue = [fields: [ project: [id: projectid],	
						summary: Title,
						description: 'New JIRA Created from Jenkins.',
						issuetype: [id: issuetype],
						assignee: [name: assignTo],
						reporter: [name: issueReporter]]]
		def Issues = [issueUpdates: [Issue]]
		response = jiraNewIssues issues: Issues
		// echo response.successful.toString()
		// echo response.data.toString()
		echo """${response}"""
	}
	}
	else {
	echo "Build is successfull, no JIRA ticket logged."
	} */
}
def buildModule(String moduleName, String branchName, String dockerRegistry)
{
	build job: """e-commerce-solution-${moduleName}""", 
	parameters: [string(name: 'moduleName', value: moduleName),
				string(name: 'dockerRegistry', value: dockerRegistry)]
}
/*get changed files path from the cahngeset(commits)*/
def getChangedModulePath()
{
    def changeLogSets = currentBuild.changeSets;
	if (changeLogSets.size() != 0)
	{
		def filepaths = '';
		for (int i = 0; i < changeLogSets.size(); i++) 
		{
			def entries = changeLogSets[i].items
			for (int j = 0; j < entries.length; j++) 
			{
				def entry = entries[j]
				echo "${entry.commitId}"
				def files = new ArrayList(entry.affectedFiles)
				for (int k = 0; k < files.size(); k++) 
				{
					def file = files[k]
					if (filepaths == null)
					{
						filepaths =file.path
					}
					else
					{
						filepaths = filepaths + ",${file.path}"
					}
				}
			}
		}
		return filepaths.trim();
	}
	else
	{
		error "No commits. No build"
	}
}
/*get list of changed modules path*/
def getModule()
{
    def dirname='';
	def filepaths = getChangedModulePath().tokenize(",")
	for (int i = 0; i < filepaths.size(); i++) 
	{
		if(dirname == '')
		{
			dirname = getbasepath(filepaths[i].trim());
		}
		else
		{
			dirname = dirname.trim() + "," + getbasepath(filepaths[i].trim());
		}
	}
	dirname = removeDuplicateModules(dirname.trim())
	return dirname.trim();
}
/*Extracts root module directory name from the path*/
def getbasepath(String path)
{
    def dirname = sh(returnStdout: true, script: """BASE_DIRECTORY=\$(echo "${path}" | cut -d "/" -f1)
                                                    echo \$BASE_DIRECTORY""")
    return dirname.trim();
}
/*removes duplicate module name from the list*/
def removeDuplicateModules(String path)
{
    def dirname = sh(returnStdout: true, script: """BASE_DIRECTORY=\$(echo "${path}" | tr ',' '\\n' | sort -u | tr '\\n' ',')
                                                    echo \$BASE_DIRECTORY""")
    return dirname.trim();                                              
}
