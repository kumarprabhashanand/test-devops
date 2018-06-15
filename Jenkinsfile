node {
    def app

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
    }

    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */
        sh 'echo "mvn installing"'
        sh 'mvn clean install'
        sh 'echo "building image"'
        app = docker.build("nik0112/test-devops")
    }

    stage('Test image') {
        /* We test our image with a simple smoke test:
         * Run a curl inside the newly-build Docker image */

        app.inside {
            sh 'echo "testing"'
        }
    }

    stage('Push image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
        docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }
    
    stage('run image') {
        sh 'echo "starting container"'
        sh 'sudo docker run --net=host -d -p 9091:9091 nik0112/test-devops'
    }
}
