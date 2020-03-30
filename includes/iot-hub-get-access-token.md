---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 7f7dc1483002c2bdfe3227a8aade8dbf2a8da417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70803011"
---
## <a name="obtain-an-azure-resource-manager-token"></a>Obtenha um token Do Gestor de Recursos Azure
O Azure Ative Directory deve autenticar todas as tarefas que executa em recursos utilizando o Gestor de Recursos Azure. O exemplo aqui apresentado utiliza a autenticação de senha, para outras abordagens ver [pedidos autenticados do Gestor][lnk-authenticate-arm]de Recursos Azure .

1. Adicione o seguinte código ao método **Principal** em Program.cs para obter um símbolo da AD Azure utilizando o id de aplicação e a palavra-passe.
   
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
2. Crie um objeto **ResourceManagementClient** que utilize o símbolo adicionando o seguinte código ao fim do método **Principal:**
   
    ```csharp
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. Crie ou obtenha uma referência ao grupo de recursos a que está a utilizar:
   
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