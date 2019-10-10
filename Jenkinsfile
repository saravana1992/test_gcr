node {
    def app

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
    }

    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */
        sh "docker build -t eu.gcr.io/qwiklabs-gcp-gcpd-9e814583b62e/app ."
    }


    stage('Push image') {
         /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
        docker.withRegistry('https://eu.gcr.io', 'gcr:test') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }
}
