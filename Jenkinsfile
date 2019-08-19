pipeline {
	agent any
	parameters {
		string(name: 'GIT_HTTPS_PATH', defaultValue: 'https://github.com/tavisca-vshah/WebApiTests.git')
		string(name: 'TEST_FILE_PATH', defaultValue: 'WebApi.Tests/WebApi.Tests.csproj')
		string(name: 'SOLUTION_FILE_PATH', defaultValue: 'WebApi/WebApi.sln')
		string(name: 'SOLUTION_NAME', defaultValue: 'WebApi')
		string(name: 'DOCKER_REPO_NAME',defaultValue:'vshahks4578/webapi')
		string(name: 'IMAGE_VERSION', defaultValue:'latest')
		string(name: 'SONAR_PROJECT_TOKEN', defaultValue: '', description: 'Path to the Solution')
	}

	stages {
		stage('Build') {
			steps {
				bat '''

				echo "----------------------------Build Project Started-----------------------------"
				dotnet C:/install_directory/SonarScanner.MSBuild.dll begin /k:"webapi" /d:sonar.host.url="http://localhost:9000" /d:sonar.login="%SONAR_PROJECT_TOKEN%"
				dotnet build %SOLUTION_FILE_PATH% -p:Configuration=release -v:n
				echo "----------------------------Build Project Completed-----------------------------"

				echo "----------------------------Test Project Started-----------------------------"
				dotnet test %TEST_FILE_PATH%
				echo "----------------------------Test Project Completed-----------------------------"
				dotnet C:/install_directory/SonarScanner.MSBuild.dll end /d:sonar.login="%SONAR_PROJECT_TOKEN%"
				echo "----------------------------Publishing Project Started-----------------------------"
				dotnet publish %SOLUTION_FILE_PATH% -c Release -o ../publish
				echo "----------------------------Publishing Project Completed-----------------------------"

				echo "----------------------------Docker Image Started-----------------------------"
				docker build -t %DOCKER_REPO_NAME%:%IMAGE_VERSION% --build-arg project_name=%SOLUTION_NAME%.dll .
				echo "----------------------------Docker Image Completed-----------------------------"
				'''
			}
		}

            stage('Deploy') {
            	steps {

            		    withCredentials([usernamePassword(credentialsId: 'dockerHub', 
            			passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
            			bat "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
            			bat "docker push %DOCKER_REPO_NAME%:%IMAGE_VERSION%"	
            		}
            	}
            }
        }
    }