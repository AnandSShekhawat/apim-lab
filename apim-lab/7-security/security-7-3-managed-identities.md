## Continuation for Exercise 7,Task 2: Managed Identities

In Azure, an Active Directory identity can be assigned to a managed resource such as an Azure Function, App Service or even an Azure API Management instance. Once an identity is assigned, it has many capabilities to work with other resources that leverage Azure AD for authentication, much like a service principal.

### Task 2.1: Register Azure API Management with Active Directory

1. Navigate to your **API management** instance, select **Managed identities** **(1)** from the pan, set the Status to **On** **(2)**, and click on **Save** **(3)** system-assigned identity.

    ![Register APIM](media/managed-identitiesa.png)

1. On **Enabled system assigned managed identity** pop-up click on **Yes**.

   > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
   > - If you receive a success message, you can proceed to the next task.
   > - If not, carefully read the error message and retry the step, following the instructions in the lab guide. 
   > - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.
         
    <validation step="114baa5f-6313-469d-932a-033f7744f3d2" />

## Key Vault 

### Task 2.2: Create Key Vault and add a secret

1. Search for **Key Vault** in the Azure portal and Select **Create**.
1. Select Resource Group: **apim-rg (1)**
1. Enter Key Vault Name: **kv-dev-hol-ms-<inject key="Deployment ID" enableCopy="false" />** **(2)**
1. Select the Pricing tier: **Standard (3)**

    ![Create Key Vault](media/8a.png)

1. Click on **Next**.
1. In the **Access configuration** tab, check the box for **vault access policy (1)** and click on **Review + Create (2)**.

    ![Create Key Vault](media/9a.png)

1. In **Review + Create** tab, click on **Create**.

1. Once created, click on **Go to resource**, this will open recently created **key-vault**.
  
1. Next, select **Secrets (1)** under Objects on the left hand pane, and click on **Generate/Import (2)**.

      ![Create Key Vault](media/mapi93a.png)

1. Provide the below details and click on **Create**.
   
    - Name:`favoritePerson`
    - Secret value: `3`

   > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
   > - If you receive a success message, you can proceed to the next task.
   > - If not, carefully read the error message and retry the step, following the instructions in the lab guide. 
   > - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.
         
    <validation step="c496182a-286f-4509-b2e7-43d5c1dd6403" />

### Task 2.3: Access policy and principal assignment

1. Select **Access policies (1)** from the left pane and click on **+ Create (2)**.

      ![Create Key Vault](media/mapi94.png)

1. Select the `Get` operation from the list of Secret permissions and click on **Next**.

      ![Create Key Vault](media/12.png)

1. Select the principal and search for **apim-dev-hol-ms-<inject key="Deployment ID" enableCopy="false" />** which is the name of your **Azure API Management instance** and select it. Click on **Next**

    ![Create Key Vault](media/13a.png)

1. Click on Next again and click on **Create**.

    You should see something like this:

    ![Create Key Vault](media/14.png)

### Task 2.4: Azure API Management, Key Vault and Managed Service Identity

1. Go back to your **APIM**.
1. Navigate to the **APIs** tab from the left pane.
1. Add a new **Operation(+ Add Operation)** to the **Star Wars** API and choose **Original** in the dropdown and provide the following values:
  
    - **Display Name: Get Favorite Person**
    - **URL: /favorite** 

      ![New operation](media/15.png)

1. Click on **Save**.

1. Navigate to the **Inbound processing (1)** tab and select **Policy code editor (2)**in this operation.

    ![Create Key Vault](media/14a.png)
   
1. Update the policies for **this** new operation, and click on **Save**, and update the **Key vault name** in the `set-url` value.

    ```xml
    <!--
        - Policies are applied in the order they appear.
        - Position <base/> inside a section to inherit policies from the outer scope.
        - Comments within policies are not preserved.
    -->
    <!-- Add policies as children to the <inbound>, <outbound>, <backend>, and <on-error> elements -->
    <policies>
        <!-- Throttle, authorize, validate, cache, or transform the requests -->
        <inbound>
        <base />
        <send-request mode="new" response-variable-name="secretResponse" timeout="20" ignore-error="false">
            <set-url>https://{your-keyvault-name}.vault.azure.net/secrets/favoritePerson/?api-version=7.0</set-url>
            <set-method>GET</set-method>
            <authentication-managed-identity resource="https://vault.azure.net" />
        </send-request>
        <set-variable name="favoritePersonRequest" value="@{
            var secret = ((IResponse)context.Variables["secretResponse"]).Body.As<JObject>();
            return "/people/" + secret["value"].ToString() + "/";
        }" />
        <rewrite-uri template="@((string)context.Variables["favoritePersonRequest"])" />
        </inbound>
        <!-- Control if and how the requests are forwarded to services  -->
        <backend>
            <base />
        </backend>
        <!-- Customize the responses -->
        <outbound>
            <base />
        </outbound>
        <!-- Handle exceptions and customize error responses  -->
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

### Task 2.5: Test the operation

1. Sign in to the developer portal and test this new operation
1. Notice the request URL will be similar to: **https://apim-dev-hol-ms-<inject key="Deployment ID" enableCopy="false" />.azure-api.net/sw/favorite**

   >**Note:** Ensure to select the product as **Unlimited** while testing.

---
### Summary 
In this Task, Azure API Management (APIM) is configured to securely access secrets from Azure Key Vault using Managed Service Identity (MSI), enhancing security and enabling the retrieval of secrets for API management operations, as demonstrated during testing in the developer portal.

### You have successfully completed the exercise. Click on **Next >>** to proceed with the next exercise.