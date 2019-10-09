node {
    def app

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
    }

    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */
        sh "docker build -t qwiklabs-gcp-ffa7d3a913f8d43d/app ."
    }


    stage('Push image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
        docker.withRegistry('https://registry.hub.docker.com', 'qwiklabs-gcp-ffa7d3a913f8d43d') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }
}
