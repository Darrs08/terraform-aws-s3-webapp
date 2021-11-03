@Library('terraformLibrary') _
pipeline {
   agent any
   tools {
      terraform 'terraform1.0.9'
   }
   parameters {
      booleanParam(
         name: 'destroy',
         defaultValue: false,
         description: 'Destroy Terraform build?'
      )
   }
   environment {
      AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
      AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
      awsRegion = 'us-east-1'
   }
   stages {
      stage ('Terraform Init') {
         when {
            equals expected: false, actual: params.destroy
         }        
         steps {
            sh 'terraform init -migrate-state -input=false'
            sh "terraform plan -input=false -out tfplan "
            sh 'terraform show -no-color tfplan > tfplan.txt'
         }
      }
      stage ('Terraform Plan') {
         when {
            equals expected: false, actual: params.destroy
         }  
         steps {
            script {
               def plan = readFile 'tfplan.txt'
               input message: "Do you want to apply the plan?",
               parameters: [text(name: 'Plan', description: 'Please review the plan', defaultValue: plan)]
            }
         }
      }
      stage ('Terraform Apply') {
         when {
            equals expected: false, actual: params.destroy
         }  
         steps {
            sh "terraform apply -input=false tfplan"
         }
      }
      stage('Terraform Destroy') {
         when {
            equals expected: true, actual: params.destroy
         }        
         steps {
            sh "terraform destroy --auto-approve"
         }
      }
   }
}
