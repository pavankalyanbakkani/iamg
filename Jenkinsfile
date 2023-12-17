timestamps {
    node('built-in') {
        
       
        stage('CleanWorkspace') {
            cleanWs()
        }
        parameters {
            choice(name: 'ENVIRONMENT', choices: ['dev', 'uat', 'prod'], description: 'Select environment')
        }
        stage('Checkout') {
            checkout([$class: 'GitSCM',
                branches: [[name: 'main']],
                doGenerateSubmoduleConfigurations: false,
                extensions: [],
                submoduleCfg: [],
                userRemoteConfigs: [[credentialsId: 'github-2',
                url: 'https://github.com/pavankalyanbakkani/IAM']]
            ])
        }
        stage('Install Python') {
    steps {
        script {
            sh 'sudo apt-get update && sudo apt-get install -y python3'
        }
    }
        stage('ACTION/DeACTION') {
            // Install required packages:
            sh 'python -m pip install --user boto3'
          
            

            // Change directory and set AWS profile and region based on the environment
            sh '''
                if [ "$ENVIRONMENT" == "dev" ]; then
                    export AWS_PROFILE=dev
                    export AWS_DEFAULT_REGION=${AWS_REGION}
                elif [ "$ENVIRONMENT" == "uat" ]; then
                    export AWS_PROFILE=uat
                    export AWS_DEFAULT_REGION=${AWS_REGION}
                elif [ "$ENVIRONMENT" == "prod" ]; then
                    export AWS_PROFILE=prod
                    export AWS_DEFAULT_REGION=${AWS_REGION}
                else
                    echo "Invalid environment specified"
                    exit 1
                fi

                ${python}/bin/python iam.py   # Pass the region parameter to the script using ${AWS_REGION}
            '''
        }
        stage('Archive Artifacts') {
            // Archive artifacts for the chosen AWS profile
            archiveArtifacts allowEmptyArchive: true,
                artifacts: "**/*.csv",
                caseSensitive: true,
                defaultExcludes: false,
                fingerprint: true,
                onlyIfSuccessful: true
        }
    }
}
