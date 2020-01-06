# s3-kms-cmk-encryption-sse-kms

A demo of how to use KMS customer master key (CMK) to encrypt/decrypt S3 data (SSE-KMS).

## Getting Started

1. Create IAM resources and CloudFormation stack for lab student:

    ```ps1
    $group="students"
    $user="student"
    $password="password"

    aws iam create-group --group-name $group
    aws iam put-group-policy --group-name $group --policy-name "lab" --policy-document file://./infrastructure/policy.json
    aws iam create-user --user-name $user
    aws iam create-login-profile --user-name $user --password $password
    aws iam add-user-to-group --group-name $group --user-name $user

    aws cloudformation create-stack --stack-name "cloudacademylabs" --template-body file://.//infrastructure/cloudformation.yaml --capabilities "CAPABILITY_IAM" --on-failure "DO_NOTHING"
    do {
        Start-Sleep 5
        $response=aws cloudformation describe-stacks --stack-name "cloudacademylabs"
    } while ("$response".Contains("CREATE_IN_PROGRESS"))
    $response
    aws cloudformation describe-stack-resources --stack-name "cloudacademylabs"

    # Update stack
    # aws cloudformation update-stack --stack-name "cloudacademylabs" --template-body file://.//infrastructure/cloudformation.yaml --capabilities "CAPABILITY_IAM"
    ```

    ```sh
    group="students"
    user="student"
    password="password"

    aws iam create-group --group-name $group
    aws iam put-group-policy --group-name $group --policy-name "lab" --policy-document file://./infrastructure/policy.json
    aws iam create-user --user-name $user
    aws iam create-login-profile --user-name $user --password $password
    aws iam add-user-to-group --group-name $group --user-name $user

    aws cloudformation create-stack --stack-name "cloudacademylabs" --template-body file://.//infrastructure/cloudformation.yaml --on-failure "DO_NOTHING" --capabilities "CAPABILITY_IAM" "CAPABILITY_NAMED_IAM"
    response=$(aws cloudformation describe-stacks --stack-name "cloudacademylabs")
    while echo $response | grep -q "CREATE_IN_PROGRESS"; do
        sleep 5
        response=$(aws cloudformation describe-stacks --stack-name "cloudacademylabs")
    done
    echo $response
    aws cloudformation describe-stack-resources --stack-name "cloudacademylabs"

    # Update stack
    # aws cloudformation update-stack --stack-name "cloudacademylabs" --template-body file://.//infrastructure/cloudformation.yaml --capabilities "CAPABILITY_IAM"
    ```

## Tearing Down

1. Delete any resources created during the lab.

2. Delete CloudFormation stack and IAM resources for lab student:

    ```ps1
    aws cloudformation delete-stack --stack-name "cloudacademylabs"
    do {
        Start-Sleep 5
        $response=aws cloudformation describe-stacks --stack-name "cloudacademylabs"
    } while ("$response".Contains("DELETE_IN_PROGRESS"))
    $response

    aws iam remove-user-from-group --group-name $group --user-name $user
    aws iam delete-login-profile --user-name $user
    aws iam delete-user --user-name $user
    aws iam delete-group-policy --group-name $group --policy-name "lab"
    aws iam delete-group --group-name $group
    ```

    ```sh
    aws cloudformation delete-stack --stack-name "cloudacademylabs"
    response=$(aws cloudformation describe-stacks --stack-name "cloudacademylabs")
    while echo $response | grep -q "DELETE_IN_PROGRESS"; do
        sleep 5
        response=$(aws cloudformation describe-stacks --stack-name "cloudacademylabs")
    done
    echo $response

    aws iam remove-user-from-group --group-name $group --user-name $user
    aws iam delete-login-profile --user-name $user
    aws iam delete-user --user-name $user
    aws iam delete-group-policy --group-name $group --policy-name "lab"
    aws iam delete-group --group-name $group
    ```