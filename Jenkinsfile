node {

    stage('Preparation') {
        catchError(buildResult: 'SUCCESS') {
            sh 'docker stop samplerunning'
            sh 'docker rm samplerunning'
        }
    }

    stage('Build') {
        build 'BuildSampleApp'
    }

    stage('Results') {
        build 'TestSampleApp'
    }

    stage('Deploy Production') {
        sshagent(credentials: ['dmz010-ssh']) {
            sh '''
                docker save sampleapp | \
                ssh -o StrictHostKeyChecking=no murtaza@192.0.2.10 "docker load"

                ssh -o StrictHostKeyChecking=no murtaza@192.0.2.10 "
                    docker stop sampleapp-prod 2>/dev/null || true
                    docker rm sampleapp-prod 2>/dev/null || true

                    docker run -d \
                      --name sampleapp-prod \
                      -p 5050:5050 \
                      sampleapp
                "
            '''
        }
    }
}
