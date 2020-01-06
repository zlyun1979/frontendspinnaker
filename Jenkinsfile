node {
    def app

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
        //version = sprintf("%04d", env.BUILD_NUMBER.toInteger())
        println "Start building version ${env.BUILD_NUMBER}"

        sh 'echo BUILD_NUMBER: ${BUILD_NUMBER}'
        sh 'BUILD_TAG: ${BUILD_TAG}'
        sh 'GIT_BRANCH: ${GIT_BRANCH}'
        sh 'GIT_COMMIT: ${GIT_COMMIT}'
        sh 'GIT_URL: ${GIT_URL}'
        sh 'JOB_NAME: ${JOB_NAME}'
    }

    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */

        app = docker.build("zlyun1979/frontendspinnaker")
    }

    stage('Test image') {
        /* Ideally, we would run a test framework against our image.
         * For this example, we're using a Volkswagen-type approach ;-) */

        app.inside {
            sh 'echo "Tests passed"'
        }
    }


    stage('Create build output') {
    
        // Make the output directory.
        sh "mkdir -p output"

        // Write an useful file, which is needed to be archived.
        writeFile file: "output/usefulfile.txt", text: "This file is useful, need to archive it."

        // Write an useless file, which is not needed to be archived.
        writeFile file: "output/uselessfile.md", text: "This file is useless, no need to archive it."

    
        // Archive the build output artifacts.
        archiveArtifacts artifacts: 'output/*.txt', excludes: 'output/*.md'
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

    stage('Remove local images') {
        sh("docker rmi -f registry.hub.docker.com/zlyun1979/frontendspinnaker:${env.BUILD_NUMBER} || :")
        sh("docker rmi -f zlyun1979/frontendspinnaker:${env.BUILD_NUMBER} || :")
        sh("docker rmi -f zlyun1979/frontendspinnaker:latest || :")
    } 



}
