node {
   stage ('Preparation') {
      git 'https://github.com/christophettat/RF_Job_Balance_demo.git'
   }
      
   stage ('Test'){
      runTests()      
   }
}

void runTests() {
//  def splits = splitTests parallelism: [$class: 'TimeDrivenParallelism', mins: 4], generateInclusions: true
    def splits = splitTests parallelism: [$class: 'CountDrivenParallelism', size: 4], generateInclusions: true
  /* Create dictionary to hold set of parallel test executions. */
  def testGroups = [:]

  for (int j = 0; j < splits.size(); j++) {
    def i=j
    def split = splits[j]
    testGroups["part-${i}"] = {
        echo "Running ${i}"
      
      node {
        cleanWs()
        checkout scm
        echo "Running ${i}"
        def launchRF = ""
        
        //writeFile file: "parallel-test-excludes-${i}.txt", text: splits[i].list.join("\n")

         if (split.includes) {
          writeFile file: "parallel-test-includes-${i}.txt", text: split.list.join("\n")
          launchRF = "robot -x xout.xml --outputdir ./Results --prerunmodifier ./PythonHelpers/ExcludeTests.py:parallel-test-excludes-${i}.txt:1 ./TestCases"
          //mavenInstall += " -Dsurefire.includesFile=target/parallel-test-includes-${i}.txt"
        } else {
          writeFile file: "parallel-test-excludes-${i}.txt", text: split.list.join("\n")
          launchRF = "robot -x xout.xml --outputdir ./Results --prerunmodifier ./PythonHelpers/ExcludeTests.py:parallel-test-excludes-${i}.txt:0 ./TestCases"
          //mavenInstall += " -Dsurefire.excludesFile=target/parallel-test-excludes-${i}.txt"
        }

        echo 'Launching the tests'
        sh "cat parallel-test*.txt"
        sh launchRF
        echo 'tests have run'
        /* Archive the test results */
      junit '**/Results/xout.xml'
      archiveArtifacts artifacts: '**/parallel*.txt', fingerprint: true
      }
    }
  }
  echo "Size of Test Group is: ${testGroups.size()}"
  parallel testGroups
}
