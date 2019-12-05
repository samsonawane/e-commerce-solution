def pomfile, apiversion, imageName;
node  
{
	timestamps 
	{
		stage('Checkout Code')
		{
			try
			{
				checkout scm;
				pomfile = readMavenPom file: """${moduleName}/pom.xml""";
				apiversion = pomfile.version;
			}
			catch (e) {
				throw e
			}
			catch (error) {
					throw error
				}
		}
		/* 
		stage ('Static Code Analysis')
		{ 
			try
			{
				def scannerHome = tool 'sonar-runner';
				withSonarQubeEnv('Dockersonar') 
				{
					staticCodeAnalysis("""${moduleName}""", scannerHome, """${Dockersonar}""")

				}
			}
			catch (e) {
				throw e
			}
		}*/
		stage ('Build & Package')
		{ 
			try
			{
				imageName = buildModule("""${moduleName}""", """${dockerRegistry}""", apiversion)
			}
		catch (e) {
				throw e
			}
		}
		stage ('Check Code Quality')
		{ 
		try
			{
				/*def scannerHome = tool 'sonar-runner';
				withSonarQubeEnv('Dockersonar') 
				{
					codeCoverage("""${moduleName}""", scannerHome, """${Dockersonar}""")
				} */
			}
			catch (e) {
				throw e
			} 
		}
		stage ('Push Docker Image to Registry')
		{ 
			try 
			{
				dockerPush(imageName)
			}
			catch (e) {
				throw e
			}
		} 
	}   
}
def staticCodeAnalysis(String moduleName, String scannerHome, String sonarHosturl)
{
	sh """cd ${moduleName}
			${scannerHome}/bin/sonar-runner -D sonar.host.url=${sonarHosturl} -D sonar.login=admin -D sonar.password=admin"""
}
def codeCoverage(String moduleName, String scannerHome, String sonarHosturl)
{
	sh """cd ${moduleName}
			${scannerHome}/bin/sonar-runner -D sonar.host.url=${sonarHosturl} -D sonar.login=admin -D sonar.password=admin -D sonar.java.binaries=target/classes -D sonar.jacoco.reportPaths=target/jacoco.exec"""
}
def buildModule(String moduleName, String dockerRegistry, String apiversion)
{
	echo """docker image build for Module - ${moduleName}"""
	//imageName="""${dockerRegistry}/${moduleName}:${apiversion}"""
	imageName="""${moduleName}:${apiversion}"""
	sh """cd ${moduleName}
	#mvn clean test -U
	#mvn clean test -U -Dmaven.test.skip=true
	mvn clean package -Dmaven.test.skip=true
	sudo docker build -t ${imageName} ."""
	return imageName;
}
def dockerPush(String imageName)
{
	sh """sudo docker push ${imageName}"""
}
