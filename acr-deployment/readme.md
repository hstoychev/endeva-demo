# Azure Container Registry/ACR feature folder.  
This folder contain ACR deployment feature. If Assign Permission is select to "yes", Principal ID will be grant with Roles below in the newly deploy Container Registry:
"ownerId", "readerId", "acrImageSignerId", "acrPullId", "acrPushId", "acrQuarantineReaderId" and "acrQuarantineWriterId".

# ARM template parameters
During Azure Container Registry ARM template deployment, several parameter are required:
 
| Parameters	| Decription	| Type	| Example
| --- | --- | --- | --- |
| `containerRegistryName` |	Name of your new Azure Container Registry-ACR. | String	| azureAcrName |
| `containerRegistryLocation` |	Location for all resources. This should be in the same region as the containers to minimize data ingress costs.	| String	| westeurope |
| `containerRegistrySku` |	Tier of your Azure Container Registry. Default value is set to Standard.|	String	|Standard|
|`assignPermission`|	If you want to assign a user, group, or service principal to Azure Container Registry. Will be granted all Container Registry roles at deployment time.	|String	|Yes|
|`principaId`|	The principal ID maps to the ID inside the Active Directory. It can point to a user, service principal, or security group.	|String	|9e07c7ff-4b57-466f-8646-3f3eb3524179|
|`application`|	Tag name of the hosted application.|	String	|testTag1|
|`departmentName`	|Tag name for department owning the server.|	String|	testTag2|
|`project`|	Tag name for project name.	|String|	testTag3|
 
 
Parameter "assignPermission" is with two pre-defined values: "YES" and "NO". If we want to assign, to our new Container Registry, additional user/group or service principal object from AAD, to have roles (below), we should select "Yes" and provide Principal ID as additional parameter. If a requester provides an SPN, this will be assigned all ACR roles to perform Acr Pull/Push/Delete etc. actions against the Registry:

•	Reader
•	Contributor
•	Owner
•	acrImageSigner 
•	acrPull
•	acrPush
•	acrQuarantineReader
•	acrQuarantineWriter
