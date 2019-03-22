node {
   def mvnHome
   stage('Preparation') { // for display purposes
      // Get some code from a GitHub repository
      git 'https://github.com/christophettat/RF_Job_Balance_demo.git'
      }
   stage('Run tests') {
      // Run the maven build
    sh './run_tests.sh'
   }
   stage('Results') {
      junit '**/Results/xout.xml'
   }
}