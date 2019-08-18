pipeline{
    environment {RELEASE_ENVIRONMENT = "${params.RELEASE_ENVIRONMENT}"}
    agent { label 'master' }
    parameters{
        string(name: 'GIT_HTTPS_PATH',defaultValue: 'https://github.com/SHIVANG-HARMILAPI/WebApiTestingCICD.git',description: 'GIT HTTPS PATH')
        string(name: 'SOLUTION_PATH',defaultValue: 'WebApi.sln',description: 'Solution Path')
        string(name: 'SOLUTION_NAME',defaultValue: 'WebApi',description: 'Solution Name')
        string(name: 'TEST_PATH',defaultValue: 'TestCasesForWebApi/TestCasesForWebApi.csproj',description: 'Test File Path')
        string(name: 'PROJECT_PATH',defaultValue: 'WebApi/WebApi.csproj',description: 'Project Path')
        string(name: 'DOCKER_USERNAME',defaultValue:'shivang10',description:'Docker Username')
        string(name: 'DOCKER_PASSWORD',defaultValue:'',description:'Docker Password')
        string(name: 'IMAGE_VERSION',defaultValue:'latest',description:'Image Version')
        string(name: 'DOCKER_REPO',defaultValue:'shivang10/webapi',description:'Docker Repository Name')
        //choice(name: 'RELEASE_ENVIRONMENT', choices: 'Build\nDeploy',description: 'Tick What You Want To Do')
         }

    stages{
        stage('Build'){
          //  when{expression{params.RELEASE_ENVIRONMENT == "Build"}}
            steps{
                powershell '''
                    echo '====================Restoring Packages ================'
                    dotnet restore ${SOLUTION_PATH} --source https://api.nuget.org/v3/index.json
                    echo '=====================Packages Restoration Completed============'
                  
                    echo '====================Build Project Start ================'
                    dotnet build ${SOLUTION_PATH} -p:Configuration=release -v:q 
                    echo '=====================Build Project Completed============'
                  
                    echo '====================Testing Project Start ================'
                    dotnet test ${TEST_PATH}
                    echo '=====================Testing Project Completed============'
                  
                    echo '====================Publishing Project Start ================'
                    dotnet publish ${SOLUTION_PATH} -c Release -o ../publish
                    echo '=====================Publishing Project Completed============'
                                '''}
        }
    

         stage('Deploy') {
            steps {
                powershell '''
                echo '====================Dcoker Image Start ================'
                docker build --tag=${IMAGE_VERSION}  
                echo '=====================Docker Image Completed============'
                echo "----------------------------Deploying Project Started-----------------------------"
                docker tag images ${DOCKER_REPO}:${IMAGE_VERSION}
                docker login --username=${DOCKER_USERNAME} --password=${DOCKER_PASSWORD}
                docker push ${DOCKER_REPO}:${IMAGE_VERSION}
                echo "----------------------------Deploying Project Completed-----------------------------"
                '''
            }

        }

    }
}
