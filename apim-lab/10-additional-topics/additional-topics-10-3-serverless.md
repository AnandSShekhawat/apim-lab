## Continuation for Exercise 9,Task 3: API Proxy to Serverless

Azure Serverless (Functions and Logic Apps) can be configured to benefit from the advantages of Azure API Management.

### Task 3.1: Azure Functions

1. Create a simple function that is Triggered by an **HTTP Request**.

2. Search for **Function App**  in the portal, click on **Select** and select **Consumption**.
   
   ![](media/Pg28-funcapp.png)

   ![](media/api20a.png)

4. Enter the following details:

   - Subscription: Select the default subscription (1)
   - Resource group: **apim-rg (2)**
   - Function name : **func-<inject key="Deployment ID" enableCopy="false" />** **(3)**
   - Operating System : **Windows (4)**
   - Runtime stack : Select **.Net (5)**
   - Version : **8(LTS), isolated worker model (6)**
   - Region: **Select the default region (7)**

    ![](media/func-1712634.png)

5. On the **Review + Create (8)** tab, click on **Create**.

6. Once the Resource is created click on **Go to Resource**.
   
7. Open Visual Studio Code from desktop.

9. Click on **Azure shaped icon (1)** and click on **Azure Functions (2)** and select **Create Function..(3)**.

      ![](media/a.png)

10. Please follow these steps after clicking on Create Function:
    
    - Navigate to `C:/LabFiles/functions` and click on the **functions** folder.
    - Select a language : **C#**
    - Select a .NET runtime : **.NET 8.0 Isolated LTS**
    - Select a template for your project's first function : **HTTP trigger**
    - Provide a function name: **GetRandomColor**
    - Provide a namespace: **Select the default**
    - AccessRights: **Function**
    - Select how you would like to open your project: **Open in current window**

11. Click on **Yes, i trust the authors** when prompted.

       ![](media/api14.png)

12. Add the below mentioned code in the existing code as shown in the image below. Click on `Ctrl+S`.

      ```c#
      //string[] strColors = { "blue", "lightblue", "darkblue" };
      string[] strColors = { "green", "lightgreen", "darkgreen" };
   
      Random r = new Random();
      int rInt = r.Next(strColors.Length);
   
      return  (ActionResult)new OkObjectResult(strColors[rInt]);
      ```

      ```C#
      using Microsoft.AspNetCore.Http;
      using Microsoft.AspNetCore.Mvc;
      using Microsoft.Azure.Functions.Worker;
      using Microsoft.Extensions.Logging;

      namespace Company.Function;

      public class GetRandomColor
      {
         private readonly ILogger<GetRandomColor> _logger;

         public GetRandomColor(ILogger<GetRandomColor> logger)
         {
            _logger = logger;
         }

         [Function("GetRandomColor")]
         public IActionResult Run([HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequest req)
         {
            _logger.LogInformation("C# HTTP trigger function processed a request.");
            //string[] strColors = { "blue", "lightblue", "darkblue" };
            string[] strColors = { "green", "lightgreen", "darkgreen" };

            Random r = new Random();
            int rInt = r.Next(strColors.Length);

            return  (ActionResult)new OkObjectResult(strColors[rInt]);
         }
      }
      ```


       ![](media/T3.1S11.png)
   
13. Click on **Deploy to Azure** and follow the below steps:

       ![](media/api50.png)
    
    **Note:** If you recieve an error mentioning you are not sign in to Azure, click on Sign in from the Resources dropdown and use the environment credentials to login, and then click on Deploy to Azure. 
     
    - Select a function app: **func-<inject key="Deployment ID" enableCopy="false" />** 
    - Click on **Deploy** when prompted.
    - Wait until the deployment is succeeded

         ![](media/api13.png)

         ![](media/func01.png)

15. Lets add the function to Azure API Management. Navigate back to the **API Management service**, in the **API blade (1)** select **+ Add API (2)** and the **Function App tile (3)**.

       ![](media/api12.png)

   - Select **Full**
   - Click on the **Browse** button to get a list of Functions in the subscription

      ![](media/06a.png)

   - Select the Function App and then the Function

      ![](media/07a.png)

      ![](media/08a.png)

   - Amend the Names / Descriptions, URL suffix, and select the **Starter** and **Unlimited** for the Products

      ![](media/09a.png)

   - Click on **Create**
   - As previously added CORS policy

   - Validate the function works - either from the Azure management portal or the developer portal: Click **Test** (1), select **GET GetRandomColor** (2), and click **Send** (3).


      ![](media/10a.png)

      ![](media/11a.png)

   > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
   > - If you receive a success message, you can proceed to the next task.
   > - If not, carefully read the error message and retry the step, following the instructions in the lab guide. 
   > - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.
         
   <validation step="b25ed567-5d6a-4ddf-a9e0-dee66fcf78d7" />

### Task 3.2: Azure Logic Apps

- Create a simple logic app that is Triggered by an HTTP Request

1. Search for **Logic App** in the portal, click on **Add** and select **Consumption**.

   ![](media/Pg28-logicapp.png)

   ![](media/api30.png)
  
1. Enter the following details:

   - Subscription: Select the default subscription (1)
   - Resource group: **apim-rg (2)**
   - Logic App Name: **logicapp-<inject key="Deployment ID" enableCopy="false" />** **(3)**
   - Region : Select the regions you have used for previous exercises **(4)**
   - Click on **Review + create (5)**.

      ![](media/api11.png)

1. Click on **Create**.

1. Once the Resource is created click on **Go to Resource**, from the left menu under Development Tools select **Logic app designer** and click on **Add a trigger (2)**.

   ![](media/api10.png)

1. In the logic app designer **search (1)** and **select (2)** **when a HTTP request is received**.

   ![](media/api40.png)

   - In the Request body JSON Schema **insert the following JSON (1)**, and select **+Add an Action (2)**.

      ```
      {
      "type" : "object",
      "properties" : {
            "msg": {
               "type" : "string"
            }
      }

      }
      ```

      ![](media/apim9.png)
      ![](media/apim9a.png)

1. Search for **Azure Functions**, and select the **Azure function** that you have created previously.

   ![](media/api17.png)

   ![](media/E9T3.2S6-2.png)

1. Add a new step to send e-mail, search for **Send an email (1)**, and select **send an email (v2) (2)** under Office 365 Outlook. Click on **Sign in** and sign in using the environment credentials

   ![](media/api18a.png)

   - **To**: Specify your Email address, i.e. **<inject key="AzureAdUserEmail"></inject>** to receive the e-mail.
   - **Subject**: **Color**
   - **Body**: type **msg**, **:** and click on add dynamic content, select **msg**, type **Color**, **:** and click on add dynamic content, search **body** and select **body** which present in **Azure function**.

        ![](media/api18.png)

        ![](media/E9T3.2S7-2.png)

1. Select **+ New step**, search and select **Response**, now **save** the logic App.

   ![](media/T3.2S8a.png)

   - Use the following sample message to generate the schema of the Request body payload.  By specifying the schema, the individual fields (in this case `msg`) can be extracted and referred to in the subsequent logic

      ```json
      {
      "msg": "text"
      }
      ```

1. Lets add the function to API Management. In the API blade select **+ Add API (1)** and the **Logic App (2)** tile

   ![](media/addapi.png)

   - Select the **Browse** button to get a list of **Logic Apps** in the subscription

      ![](media/browse.png)

   - Select the **Logic App**, and choose **Select**.

      ![](media/logicapp.png)

   - On the **Create from Logic App**, select **Full**. Amend the Names / Descriptions, Add URL suffix as **logicapp**, select the Products(Starter, Unlimited), and select **Create**.

      ![](media/create.png)

    - As previously add CORS policy

   - Validate the Logic App works - either from the Azure management portal or the developer portal. Click **Test** (1), select **GET GetRandomColor** (2), and click **Send** (3).

      ![](media/18a.png)

      ![](media/19a.png)

   - Go to **Run history**, Click the latest run and verify all steps in the flow are completed successfully.

      ![](media/20a.png)

   - Check the email was sent

      ![](media/21.png)

--- 
   > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
   > - If you receive a success message, you can proceed to the next task.
   > - If not, carefully read the error message and retry the step, following the instructions in the lab guide. 
   > - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.
         
   <validation step="c60e228d-7a4a-49bb-a8da-dfa8c1415230" />

## Summary
In this task, you have integrated Azure Functions and Logic Apps with Azure API Management, exposing them as APIs with management capabilities. you have configured, tested, and audited these serverless resources within API Management for seamless integration with other services.

### Now, click on Next from the lower right corner to move on to the next page for further tasks.
