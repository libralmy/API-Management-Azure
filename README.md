# API-Management-Azure [link](https://learn.microsoft.com/en-us/azure/api-management/api-management-key-concepts)
  - Azure API Management helps securely expose services hosted on and outside of Azure as APIs
  - Azure API Management is made up of an API gateway, a management plane, and a developer portal. API gateway can verify the IP range, credentials like JWT tokens and certificates presented with requests

## Prerequisites

1. An API Management instance [here](https://learn.microsoft.com/en-us/azure/api-management/get-started-create-service-instance-cli)
2. An Azure AD tenant

## Register and Configure the Client and DPS Role Management Resource in AAD 
![image](https://user-images.githubusercontent.com/20976896/220787545-b568cba2-9ab2-4196-82a0-b4bc10a75a61.png)
1. DPS Role Management App <br />
  a Change the value [accessTokenAcceptedVersion] field from null (which defaults to: 1) to 2<br />
  b Create App role(DPS.Access)<br />
  c Expose the API and set the Application ID URI(api://xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)<br />
2. Client App<br />
  a  Create a secret (copy the secret once it is generated as you won’t be able to view it again after you leave this page)<br />
  b  Grant the Client App the permission(app role-DPS.Access) through API permission<br />
  c  Grant admin consent after create the permission<br />
  
 ## Configure APIM Instance
 ![image](https://user-images.githubusercontent.com/20976896/220787594-b1812b95-ee71-4634-b611-0c63a4aecd0e.png)
 1. Create APIM and test api [here](https://learn.microsoft.com/en-us/azure/api-management/import-and-publish)
 2. Applying the JWT validation policy on the operation-level
   ```sh
   <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized API Call">
   <openid-config url="https://login.microsoftonline.com/{TenantID}/v2.0/.well-known/openid-configuration" />
    <required-claims>
     <claim name="roles">
       <value>DPS.Access</value>
     </claim>
   </required-claims>
  </validate-jwt>
   ```
   
  ## Postman Test
  1. Request Bearer Token [You have to use v2.0 or the app role won't show up in the token]
    POST: https://login.microsoftonline.com/{TenantID}/oauth2/v2.0/token
  ![image](https://user-images.githubusercontent.com/20976896/220791546-ccc71d2e-7d05-4f6e-bf80-43d83af5bb3b.png)
  2. Pass the JWT in the [Authorization] header in another request to the APIM published API operation
  ![image](https://user-images.githubusercontent.com/20976896/220792086-bee54c97-b108-4e47-8603-41bd3bdd255b.png)
  
  ## References
  [Protect an API in Azure API Management using OAuth 2.0 authorization with Azure Active Directory](https://learn.microsoft.com/en-us/azure/api-management/api-management-howto-protect-backend-with-aad)
  


