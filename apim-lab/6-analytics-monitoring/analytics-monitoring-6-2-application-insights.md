## Continuation for Exercise 6, Task 2: How to integrate Azure API Management with Azure Application Insights

Azure API Management allows for easy integration with Azure Application Insights - an extensible service for web developers building and managing apps on multiple platforms. This guide walks through every step of such integration and describes strategies for reducing performance impact on your Azure API Management instance.

### Task 2.1: Create an Azure Application Insights instance

Before you can use Azure Application Insights, you first need to create an instance of the service. The naming convention for Application Insights is: `appi-<environment>-<region>-<application-name>-<owner>-<instance>`

1. Open the **Azure portal** and navigate to **Application Insights**.

    ![App Insights Create Instance](media/04.png)

2. Click **+ Create**, then fill in the form.

    - Resource Group: **apim-rg**
     
   - Name: **appi-dev-hol-ms-<inject key="Deployment ID" enableCopy="false" />**

        ![App Insights Create Instance](media/appl_insight.png)

3. Click **Review + Create**, followed by **Create**.

   > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
   > - If you receive a success message, you can proceed to the next task.
   > - If not, carefully read the error message and retry the step, following the instructions in the lab guide. 
   > - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.

    <validation step="c50fd98d-9969-4471-95d7-ae0959ed081f" />

### Task 2.2: Create a connection between Azure Application Insights and Azure API Management service instance

1. Navigate to your **APIM** instance in the **Azure portal**.
2. Select **Application Insights** from the menu on the left.
3. Click **+ Add**.

      ![APIM App Insights Logger](media/06.png)

4. Select the **Application Insights** resource we created in the previous task and click on **Create**.

     ![APIM App Insights Logger](media/07.png)
   
5. You have just created an Azure Application Insights logger with an instrumentation key. It should now appear in the list.

    ![APIM App Insights Logger](media/08.png)

    > Behind the scenes, a [Logger](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/logger/createorupdate) entity is created in your Azure API Management instance, containing the Connection String of the Application Insights instance.

   > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
   > - If you receive a success message, you can proceed to the next task.
   > - If not, carefully read the error message and retry the step, following the instructions in the lab guide. 
   > - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.

    <validation step="65dfec7c-e946-478a-8012-7430913523bd" />

### Task 2.3: Enable Application Insights logging for your API

1. Select **APIs** from the menu on the left.
2. Click on the **Colors** API.
3. Go to the **Settings** tab from the top bar.
4. Scroll down to the **Diagnostics Logs** section.
5. On the **Application Insights** tab check the **Enable(1)** box.
6. In the **Destination (2)** dropdown select the logger you just added in the **Application Insights** blade.
7. Set sampling to **100 (3)** to capture all events.
8. Check the **Always log errors (4)** checkbox.

    ![APIM API App Insights Logger](media/pg21T2.3S8-1005.png)

9. Click on **Save (5)**.

    > Overriding the default value **0** in the **Number of payload bytes to log** field may significantly decrease the performance of your APIs.

    > Behind the scenes, a [Diagnostic](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/diagnostic/createorupdate) entity named 'applicationinsights' is created at the API level.

### What data is added to Azure Application Insights

Azure Application Insights receives:

- *Request* telemetry item for every incoming request (*frontend request*, *frontend response*),
- *Dependency* telemetry item for every request forwarded to a backend service (*backend request*, *backend response*),
- *Exception* telemetry item, for every failed request.

A failed request is a request which:

- failed because of a closed client connection, or
- triggered an *on-error* section of the API policies, or
- has a response HTTP status code matching 4xx or 5xx.

### Generating Test Requests

Any request you make to the Colors API in Azure API Management will be subject to being received by Application Insights (recall the 100% sampling). To generate a large amount of requests quickly, you can invoke the API via the [Colors](https://colors-web.azurewebsites.net) website. As we are presently caching the output for 15 seconds, you may get a lot of requests with the same color. Please feel free to disable the output caching in the Colors API if you would like to see more variety.

### Task 2.4: Viewing Azure Application Insights Data

1. Go back to the **Application Insights** blade and click on the Application Insights instance.
1. On the left hand side pane, click on the **Transaction search (1)** under **Investigate** to see details on a transactional level.
1. In the Application Insights instance, you should be able to see logs and metrics after a few seconds.
1. Unselect **Availability (2)** from Event Types.

    ![APIM API App Insights Logger](media/mapi61.png)

    > **Note:** The logs may take up to 30 minutes to appear. Kindly continue with the next exercise and check back later.

### Performance implications and log sampling

> Logging all events may have serious performance implications, depending on incoming request rate, payload size, etc.

Based on internal load tests, enabling this feature caused a 40%-50% reduction in throughput when the request rate exceeded 1,000 requests per second. Azure Application Insights is designed to use statistical analysis for assessing application performances. It is not intended to be an audit system and is not suited for logging each individual request for high-volume APIs.

You can manipulate the number of requests being logged by adjusting the **Sampling** setting (see the steps above). 100% means all requests are logged, while 0% reflects no logging at all. **Sampling** helps to reduce the volume of telemetry, effectively preventing significant performance degradation, while still carrying the benefits of logging.
Sampling is an effective tool in diagnosing often general operational issues. For example, sampling can identify connectivity or integration issues as these would often occur in high quantity, not singular instances. A sampling rate of 50% is as effective in diagnosing such issues as 100% is.

Skipping logging of headers and body of requests and responses will also have a positive impact on alleviating performance issues.

### Now, click on Next from the lower right corner to move on to the next page for further tasks.
