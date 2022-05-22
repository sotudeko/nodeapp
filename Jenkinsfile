pipeline {
  agent any

  environment {
    SBOM_FILE = "bom.xml"
  }

  stages {

    stage('Build') {
      steps {
        sh 'npm install'
      }
      post {
        success {
          sh "npx auditjs@latest sbom > ${SBOM_FILE}"
        }
      }
    }

    stage('Nexus IQ Scan - Auditjs') {
      steps {   
        script {    
          try {
              def policyEvaluation = nexusPolicyEvaluation failBuildOnNetworkError: true, 
                                      iqApplication: selectedApplication('nodeapp-aj'), 
                                      iqScanPatterns: [[scanPattern:'${SBOM_FILE}']], 
                                      iqStage: 'build', 
                                      jobCredentialsId: 'admin'

              echo "Nexus IQ scan succeeded: ${policyEvaluation.applicationCompositionReportUrl}"
              IQ_SCAN_URL = "${policyEvaluation.applicationCompositionReportUrl}"
          }
          catch (error) {
              def policyEvaluation = error.policyEvaluation
              echo "Nexus IQ scan vulnerabilities detected', ${policyEvaluation.applicationCompositionReportUrl}"
              throw error
          }
        }
      }
    }

    stage('Create package'){
      steps {
        script {
          sh 'npm pack'
        }
      }
    }

    stage('File listing'){
      steps {
        script {
          sh 'ls -l'
        }
      }
    }  
  }
}   
