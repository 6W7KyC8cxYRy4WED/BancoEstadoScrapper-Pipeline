pipeline {
    agent{
        label 'PROCESSOR02.302.local'
    }
    stages{
        stage('Clone BancoEstadoScrapper project.'){
            steps{
                ws('workspace/BancoEstadoScrapper/BancoEstadoScrapper-Project'){
                    cleanWs();
                    checkout scmGit(branches: [[name: 'master']], userRemoteConfigs: [[credentialsId:  'GitHub', url: 'https://github.com/6W7KyC8cxYRy4WED/BancoEstadoScrapper.git']]);
                    script {
                        env.GIT_COMMIT_VERSION = sh (script: 'git log -1 --pretty=%B ${GIT_COMMIT}', returnStdout: true).trim()
                    }
                }
            }
        }
        stage('Clone BancoEstadoScrapper-Nuspec project.'){
            steps{
                ws('workspace/BancoEstadoScrapper/BancoEstadoScrapper-Nuspec'){
                    cleanWs();
                    checkout scmGit(branches: [[name: 'master']], userRemoteConfigs: [[credentialsId:  'GitHub', url: 'https://github.com/6W7KyC8cxYRy4WED/BancoEstadoScrapper-Nuspec.git']]);
                    script {
                        sh (script: "sed -i 's/{VERSION}/${GIT_COMMIT_VERSION}/g' .nuspec", returnStdout: true)
                        sh (script: "cp .nuspec ../BancoEstadoScrapper-Project/BancoEstadoScrapper/.nuspec", returnStdout: true)
                    }
                }
            }
        }
        stage('Publish NuGet package.'){
            steps{
                ws('workspace/BancoEstadoScrapper/BancoEstadoScrapper-Project/BancoEstadoScrapper'){
                    withCredentials([string(credentialsId: 'BaGetter-ApiKey', variable: 'APIKEY')]) {
                        script {
                            sh (script: "mono /usr/local/bin/nuget.exe pack", returnStdout: true)
                            sh (script: 'mono /usr/local/bin/nuget.exe push -Source https://nuget.cloud.302.cl/v3/index.json BancoEstadoScrapper.${GIT_COMMIT_VERSION}.nupkg -ApiKey $APIKEY -SkipDuplicate', returnStdout: true)
                        }
                    }
                }
            }
        }
    }
}