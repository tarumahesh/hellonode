node {
    def app

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
    }

    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */

        app = docker.build("tarumahesh/hellonode")
            }
    stage('Get Image id') {
        steps {
            script {
                def IMAGE_ID = sh(script: "docker images | grep -E '^tarumahesh.*hellonode' | head -1 | awk '{print \$3}'", returnStdout:true).trim()
                env.IMAGE_ID = IMAGE_ID
                    }
               }
 }

    stage('Get Image Vulns - Qualys Plugin') {
        steps {
            getImageVulnsFromQualys useGlobalConfig:true,
            imageIds: env.IMAGE_ID
             }
 }

 /*   stage('Test image') {
         We test our image with a simple smoke test:
         Run a curl inside the newly-build Docker image 

        app.inside {
            sh 'curl http://localhost:8000 || exit 1'        }
    } */

    stage('Push image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
        docker.withRegistry('http://192.168.56.107:5000') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }
}
