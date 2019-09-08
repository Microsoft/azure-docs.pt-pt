---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 7f7dc1483002c2bdfe3227a8aade8dbf2a8da417
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70803011"
---
## <a name="obtain-an-azure-resource-manager-token"></a>Obter um token de Azure Resource Manager
Azure Active Directory deve autenticar todas as tarefas que você executa nos recursos usando o Azure Resource Manager. O exemplo mostrado aqui usa a autenticação de senha, para outras abordagens, consulte [autenticando Azure Resource Manager solicitações][lnk-authenticate-arm].

1. Adicione o seguinte código ao método **principal** no Program.cs para recuperar um token do Azure ad usando a ID do aplicativo e a senha.
   
    ```csharp
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.microsoftonline.com/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
   
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```
2. Crie um objeto **ResourceManagementClient** que usa o token adicionando o seguinte código ao final do método **Main** :
   
    ```csharp
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. Crie ou obtenha uma referência para o grupo de recursos que você está usando:
   
    ```csharp
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx