node {
   stage ('Preparation') {
      git 'https://github.com/christophettat/RF_Job_Balance_demo.git'
   }
      
   stage ('Test'){
      runTests()      
   }
}

void runTests(def args) {
//  def splits = splitTests parallelism: [$class: 'TimeDrivenParallelism', mins: 4], generateInclusions: true
    def splits = splitTests parallelism: [$class: 'CountDrivenParallelism', size: 4], generateInclusions: true


  /* Create dictionary to hold set of parallel test executions. */
  def testGroups = [:]

  for (int i = 0; i < splits.size(); i++) {
    def split = splits[i]
    testGroups["part-${i}"] = { 
      node {
        cleanWs()
        checkout scm
        echo "Running ${i}"
        def launchRF = "robot -x xout.xml --outputdir ./Results --prerunmodifier ./PythonHelpers/ExcludeTests.py:parallel-test-excludes-${i}.txt ./TestCases"
        
        writeFile file: "parallel-test-excludes-${i}.txt", text: split.list.join("\n")

        echo 'Launching the tests'
        sh launchRF
        echo 'tests have run'
        /* Archive the test results */
      junit '**/Results/xout.xml'
      }
    }
  }
  echo "Size of Test Group is: ${testGroups.size()}"
  parallel testGroups
}
