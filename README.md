## AWS Step Functions Waitable Pattern

[![Open in Visual Studio Code](https://open.vscode.dev/badges/open-in-vscode.svg)](https://open.vscode.dev/aws-samples/aws-step-functions-waitable-pattern)

This projects contains source code with an example of how to implement an AWS Step Function waitable pattern that you can deploy using the SAM CLI. It includes the following files and folders:

- **statemachine** - Definition for the state machine that orchestrates the change between status after the defined time is reached.
- **template.yaml** - A template that defines the application's AWS resources.
- **waitable-stepfunction.asl.json** - The file that contains the Step Function Workflow.

This sample demonstrates the power of AWS Step Functions when orchestrating time sensitive applications (e.g. send email notification to user after 2h) while keeping costs under control since you don't pay for the time that AWS Step Functions is on a wait state.

To make it reusable in real use cases, we use Amazon API Gateway as a way to trigger and send input parameters to the State Machine, and AWS DynamoDB as the persistent store for each item Status. The final workflow looks like this:

![stepfunctions_graph](/images/diagram.png)


## Deploy the sample application

The Serverless Application Model Command Line Interface (SAM CLI) is an extension of the AWS CLI that adds functionality for building and testing Lambda applications. It uses Docker to run your functions in an Amazon Linux environment that matches Lambda.

To use the SAM CLI, you need the following tools:

* SAM CLI - [Install the SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html)
* Node.js - [Install Node.js 12](https://nodejs.org/en/), including the NPM package management tool.
* Docker - [Install Docker community edition](https://hub.docker.com/search/?type=edition&offering=community)

To build and deploy your application for the first time, run the following in your shell:

```bash
sam build
sam deploy --guided
```

## Try on AWS console

1. Sign in to your AWS console at https://console.aws.amazon.com


2. Go to the Amazon API Gateway service

3. Select your new API *(e.g. aws-stepfunction-waitable-pattern)*

4. Select the **POST** method on the resources tab

5. Select the **Test** button on the right panel

6. Specify a valid json payload as the **request body**, like so:

    ```json
    {
      "StartTimestamp": "2021-06-25T17:47:00Z"
    }
    ```
    
7. Press the **Test** button. 
You should get a valid response.

    ```json
    Job Id fe520f82-8f3e-4058-bb2d-e90f271fc3d6 started successfully. For additional information on process status use GET /status.
    ```

8. Select the **GET** method under the */status* resource

9. Select the **Test** button on the right panel and press the **Test** button.
   You should get a valid response.


    ```json
    {
    "items": [
        {
          "Id": "4cb0702d-1b62-4b09-8482-fc1182ca4240",
          "StartActionAt": "2021-07-09T13:50:00Z",
          "LastUpdated": "2021-07-09T13:35:43.151Z",
          "ProcessStatus": "STARTED"
        },
        {
          "Id": "fe520f82-8f3e-4058-bb2d-e90f271fc3d6",
          "StartActionAt": "2021-07-09T13:46:00Z",
          "LastUpdated": "2021-07-09T13:45:59.462Z",
          "ProcessStatus": "COMPLETED"
        },
        {
          "Id": "46a332a6-18d5-4c17-89d5-db1eeb78baec",
          "StartActionAt": "2021-07-09T13:40:00Z",
          "LastUpdated": "2021-07-09T13:39:59.653Z",
          "ProcessStatus": "COMPLETED"
        }
      ]
    }
    ```


## Using cURL

1. Open your prefered Terminal
2. Make sure you have cURL installed

   **MacOS**

   ```bash
   brew install curl
   ```

   **Linux**

   ```bash
   sudo apt-get install curl
   ```

   **Windows - with chocolatey**

   ```bash
   choco install curl
   ```

3. Send a request to API Gateway

    ```bash
    curl --location --request POST 'REPLACE_WITH_YOUR_API_GATEWAY_ENDPOINT' \
    --header 'Content-Type: application/json' \
    --data-raw '{
        "StartTimestamp": "2021-06-25T17:47:00Z"
    }'
    ```
    You should get a valid response.

    ```json
    Job Id fe520f82-8f3e-4058-bb2d-e90f271fc3d6 started successfully. For additional information on process status use GET /status.
    ```

4. Check process status

    ```bash
    curl --location --request GET 'REPLACE_WITH_YOUR_API_GATEWAY_ENDPOINT'/status
    ```

    You should get a valid response.


      ```json
      {
      "items": [
          {
            "Id": "4cb0702d-1b62-4b09-8482-fc1182ca4240",
            "StartActionAt": "2021-07-09T13:50:00Z",
            "LastUpdated": "2021-07-09T13:35:43.151Z",
            "ProcessStatus": "STARTED"
          },
          {
            "Id": "fe520f82-8f3e-4058-bb2d-e90f271fc3d6",
            "StartActionAt": "2021-07-09T13:46:00Z",
            "LastUpdated": "2021-07-09T13:45:59.462Z",
            "ProcessStatus": "COMPLETED"
          },
          {
            "Id": "46a332a6-18d5-4c17-89d5-db1eeb78baec",
            "StartActionAt": "2021-07-09T13:40:00Z",
            "LastUpdated": "2021-07-09T13:39:59.653Z",
            "ProcessStatus": "COMPLETED"
          }
        ]
      }
      ```


## Cleanup

To delete the sample application that you created, use the AWS CLI. Assuming you used your project name for the stack name, you can run the following:

```bash
aws cloudformation delete-stack --stack-name STACK_NAME
```

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

