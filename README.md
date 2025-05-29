# Uploading-artifacts-to-S3-Bucket-using-Jenkins
pipeline {
    agent any

    stages {
        stage('Stage-1 , clean ws') {
            steps {
                cleanWs()
            }
        }
        stage('Stage-2 , Git clone scm') {
            steps {
               git branch: 'main', url: 'https://github.com/Hajaresab1992/Uploading-artifacts-to-S3-Bucket-using-Jenkins.git'
            }
        }
        stage('Stage-3 , npm install') {
            steps {
                sh 'npm install'
            }
        }
         stage('Stage-4 , artifact Gzip ') {
            steps {
                script{
                    sh """
                        #create a tem dir
                        mkdir -p /tmp/archive
                        # copy 
                        rsync -av --exclude='.git'./ /tmp/archive/
                        tar -czvf artifact-${JOB_NAME}-${BUILD_NUMBER}-${BUILD_ID}.tar.gz -C /tmp/archive .
                    """
                }
            }
        }
        stage('Stage-5 , upload Artifacts') {
            steps {
               s3Upload consoleLogLevel: 'INFO', dontSetBuildResultOnFailure: false, dontWaitForConcurrentBuildCompletion: false, entries: [[bucket: 'bingoartifacts', excludedFile: '', flatten: false, gzipFiles: false, keepForever: false, managedArtifacts: false, noUploadOnFailure: false, selectedRegion: 'ap-south-1', showDirectlyInBrowser: false, sourceFile: '*.tar.gz', storageClass: 'STANDARD', uploadFromSlave: false, useServerSideEncryption: false]], pluginFailureResultConstraint: 'FAILURE', profileName: 'artifactss3', userMetadata: []
            }
        }
        
        
    }
}


