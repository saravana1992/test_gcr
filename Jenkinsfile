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
        sh "docker push eu.gcr.io/qwiklabs-gcp-gcpd-9e814583b62e/app"
        }
}
