Implementing Traffic Shifting Using SAM and CodeDeploy
Introduction
Lambda and CodeDeploy make it possible to automatically shift incoming traffic between two function versions based on a preconfigured rollout strategy. This feature allows you to gradually shift traffic to the new function. If there are any issues with the new code, you can quickly rollback and control the impact to your application. Traffic shifting is built right into the AWS Serverless Application Model (SAM), making it easy to define and deploy your traffic shifting capabilities.

In this hands-on lab, you will learn how to use SAM and CodeDeploy to accomplish an automated rollout strategy for safe Lambda deployments.

Lab Prerequisites
Understand how to log in to and use the AWS Management Console.
Understand how to use the AWS Command Line Interface (CLI).
Understand how to use ssh from the command line.
We'll be using both the AWS console and a terminal (two terminals, eventually). Make sure you are operating the lab within the N. Virginia (us-east-1) region.

We'll be using this GitHub repository: https://github.com/linuxacademy/content-aws-sam

Download SAM Application Template
Connect to the EC2 instance via ssh using the credentials provided in the lab instructions.
Clone the GitHub repository containing the template code and sample application:
```
 git clone https://github.com/linuxacademy/content-aws-sam
```
Change to this lab's content directory:
```
 cd content-aws-sam/labs/Implementing-Traffic-Shifting-SAM-CodeDeploy
```
### Modify and Deploy SAM Template

1. Open template.yaml in an editor.
2. Under the ApiFunction resource, set the value of DeploymentPreference > Type to Linear10PercentEvery1Minute. Here is what it looks like before edits:

    ```
    DeploymentPreference:
        # AllAtOnce
        # Canary10Percent30Minutes
        # Canary10Percent5Minutes
        # Canary10Percent10Minutes
        # Canary10Percent15Minutes
        # Linear10PercentEvery10Minutes
        # Linear10PercentEvery1Minute
        # Linear10PercentEvery2Minutes
        # Linear10PercentEvery3Minutes
        Type: ??? <--------------------- FILL THIS IN
    ```
    Replace those question marks (and everything afterward on that line) with `Linear10PercentEvery1Minute`, like this:
    ```
    Type: Linear10PercentEvery1Minute
    ```
3. Save the file.
4. Validate the SAM template:
    ```
    sam validate
    ```
5. Now we can deploy the template in guided mode using this command:
    ```
    sam deploy -g
    ```
    You may accept the defaults at all of the prompts.
6. Observe the output to verify that the stack deployed successfully.
7. Note the values in the Outputs section, as these will be needed in subsequent tasks.

### Deploy a Code Change and Observe Traffic Shifting

1. Edit test.sh and specify the HTTP API invoke URL that was in the output from the SAM template:
* This will be the Value for the Key called ApiUrl. Replace the placeholder, xxxxxxxxxx, in the APIGW=http:// line.
* Test afterward by executing ./test.sh. We should see this output every half of a second: {"body": "This is version 1"} Press Ctrl c to kill the script.
2. Edit src/index.py and change the message to version 2.
3. Split the terminal window, or open up another terminal. Either way, log in (with SSH) and get into the content-aws-sam/labs/Implementing-Traffic-Shifting-SAM-CodeDeploy directory.
4. In one pane/terminal, run ./test.sh
5. In the other, run sam deploy
6. Back in the AWS console, go to CodeDeploy, select Deployments, and click the one that's showing as In progress there. We'll see that traffic shifting has begun.
7. In the terminal, we'll also see that we're getting some This is version 2 messages as traffic shifts. After ten minutes, these will be the only messages we see, since the shift to version 2 is complete.