node {
    stage 'checkout'
    git 'https://gitlab.com/RavisankarCts/hello-world.git' 
    
    stage ('build') {
      def mvn_version = 'M3'
      withEnv( ["PATH+MAVEN=${tool mvn_version}/bin"] ) {
        sh 'mvn clean install'
      }
    }

    stage('Results - 1') {
         junit '**/target/surefire-reports/TEST-*.xml'
         archive 'target/*.jar'
        }

    stage 'bake image'
    docker.withRegistry('https://registry.hub.docker.com','ramana1') {
        def image = docker.build("ramana/ramana1:${env.BUILD_TAG}",'.')
        
        stage 'test image'
        image.withRun('-p 8888:8888') {springboot ->
        sh 'while ! httping -qc1 http://localhost:8888/info; do sleep 1; done'
        git 'https://github.com/ramanurs/spring-framework-petclinic.git'
        sh 'mvn clean verify'
        }
        
        stage('Results') {
         junit '**/target/surefire-reports/TEST-*.xml'
         archive 'target/*.jar'
        }
        
        stage 'push image'
        image.push()
    }
}
