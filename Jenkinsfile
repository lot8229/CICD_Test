node {
     stage('Clone repository') {
         checkout scm
     }

     stage('Build BackEnd') {
        sh'''
        ./gradlew build
        '''
     }

//      stage('Build jar'){
//         sh'''
//         java -jar app.jar
//         '''
//      }

     stage('Build image') {
        app = docker.build("${REPOSITORY_NAME}/${CONTAINER_NAME}:latest")
     }

     stage('Push image') {
        docker.withRegistry('https://registry.hub.docker.com', 'docker-hub') {
            app.push()
        }
     }

     stage('Code Deploy') {
         sh '''docker stop ${CONTAINER_NAME} || true && docker rm ${CONTAINER_NAME} || true'''
         sh '''docker rmi -f `docker images | awk '$1 ~ /cicd_test/ {print $3}'`'''
         sh '''docker run -d -p ${PORT}:${PORT} --name ${CONTAINER_NAME} ${REPOSITORY_NAME}/${CONTAINER_NAME}:latest'''
     }
}