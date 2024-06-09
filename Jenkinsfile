pipeline{
    parameters{
        booleanParam(name:'autoApprove', defaultValue:false , description:"Automatically run apply after generating plan?")
    }
    environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    }

    agent any
    stages{
        stage("checkout"){
            steps{
                script{
                    dir("terraform"){
                        git branch: 'main' , url: 'https://github.com/Devops-13999/Jenkins_Terraform_pipeline.git'
                    }
                }
            }
            post{
                always{
                    echo "checkout stage executed"
                }
                success{
                    echo "executed successfully"
                }
                failure{
                    echo "execution failed="
                }
            }
        }
        stage("plan"){
            steps{
                sh 'pwd;cd terraform ; terraform init'
                sh 'pwd;cd terraform ; terraform plan -out tfplan'
                sh 'pwd;cd terraform ; terraform show -no-color tfplan > tfplan.txt'
                }
        }
        stage("Approve"){
            when{
                not{
                    equals expected: true , actual: params.autoApprove
                }
            }
            steps{
                script{
                    def plan = readFile 'terraform/tfplan.txt'
                    input message: "Do you want to apply the plan?",
                    parameters: [text(name: 'Plan' , description: 'please review the plam' , defaultValue: plan)]
                }
                }
            }
        stage("apply"){
            steps{
                sh 'pwd;cd terraform ; terraform apply -input=false tfplan'
                }
        }    
        
    }
}
