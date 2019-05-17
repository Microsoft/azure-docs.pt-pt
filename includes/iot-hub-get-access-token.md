---
ms.openlocfilehash: b6ea8c7b3a6374572c8bd31e3c62b788efbafcbc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "65827474"
---
## <a name="obtain-an-azure-resource-manager-token"></a>Obter um token do Azure Resource Manager
O Azure Active Directory tem de ser autenticado todas as tarefas que executar nos recursos com o Azure Resource Manager. O exemplo mostrado a seguir utiliza a autenticação de palavra-passe, para outras abordagens, consulte [pedidos de autenticação do Azure Resource Manager][lnk-authenticate-arm].

1. Adicione o seguinte código para o **Main** método em Program.cs para obter um token do Azure AD com o id da aplicação e a palavra-passe.

    ```
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

2. Criar uma **ResourceManagementClient** objeto que utiliza o token adicionando o seguinte código ao final do **Main** método:

    ```
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```

3. Criar ou obter uma referência para o grupo de recursos que está a utilizar:

    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/zh-cn/library/azure/dn790557.aspx