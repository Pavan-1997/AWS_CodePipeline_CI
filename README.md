# AWS CodePipeline CI 

Performing AWS Continuous Integration all the steps are documented as below

1. Login to AWS Console 


2. Open the CodeBuild tool from AWS Console


3. Click on Create build project -> Give Project name -> Give a Description


4. Now goto Source section -> Select the Source as GitHub -> Click on Connect to GitHub and Authenticate the GitHub using Oauth later Give the repo URL of the code in Repository URL

- AWS CodePipeline invokes the AWS CodeBuild


5. Now to the Enviroment section -> Use Managed image -> Select OS as Ubuntu -> Select Runtime(s) as Standard -> Select Image as aws/codebuild/standard:7.0 (whichever the latest) -> Use the Image version as the latest image ... -> Use Enviroment type as Linux -> User New service role

- We are using IAM Role since a service is performing an action but not user for which we would create an IAM User

-  If want to create a role manually then goto IAM -> Click on Roles -> Click on Create role -> Use the AWS service -> Search for the role with name CodeBuild and select Allow CodeBuild to call AWS services on your behalf. -> Click on Next -> Give role name and click on Create role now go back to the page of Step 5. and you should be seeing the new role name created 


6. To store sensitive information we are using AWS Systems Manager service

    Goto AWS Console and search for Systems Manager -> Click on Parameter Store on the left side -> Click on Create Parameter -> Give name like this for our naming convection easier to use /myapp/docker-cred/username -> User Tier as Standard > Select Type as SecureString -> In the Value give the username
    
    Create for Docker username, password and URL (Value : docker.io) by following the above
    
    Now goto IAM roles -> Select the IAM role that was used in Step 5. -> Click on Add Permissions and Click on Attach policies -> Search and select the role name as AmazonSSMFullAccess and click on Add permissions
    

7. Now goto Buildspec section -> Select Insert build commands -> Write the YAML file as below by clicking on Switch to editor

- checkout stage will happen automatically as we have already integrated it with GitHub in Step 4.

    Once wrting the YAML file click on Create build project
    
    Click on Start build 
    
    If getting error 2023/07/14 18:45:56 Phase context status code: COMMAND_EXECUTION_ERROR Message: Error while executing command: day-14/simple-python-app/. Reason: exit status 126 then follow below step
    
    Give additional permission to the CodeBuild -> Goto the Build projects -> Click on Edit of the project that has been created -> Enviroment -> Click on Override image -> Now check the Enable this flag ... under Priviledged

- For automatic starting of the build we will be configuring the AWS CodePipeline lalter which check for commits in AWS CodeCommit and invokes AWS CodeBuild


8. Integrating it with CodePipeline to trigger the build automatically

    Open CodePipeline from AWS Console -> Click on Create pipeline -> Give Pipeline name -> Use the New service role >  Click on Next -> Select Source provider as Github (Version 2) -> Click on connect to Github -> Give a Connection name and click on Install a new app and then authorize using your GitHUb account -> Click on Connect 
    
    Now give the Repositiory name as sqlonlinux/python_sample_CI_AWS -> Give Branch name as main -> Click on Next 
    
    Now selct the Build provider as AWS CodeBuild -> Select the Project name that you have created earlier -> Click on Next -> As of now skip the Deploy stage and click on Skip deploy stage -> Click on Create pipeline

Once the pipeline is create give a test commit and see the pipeline to be invoked 

