---
title: Como configurar identidades geridas para o cluster Azure Data Explorer
description: Saiba como configurar identidades geridas para o cluster Do Explorador de Dados do Azure.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 900bf815917a4b7c9841860d663a2183b1ab71b3
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529680"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Configure identidades geridas para o seu cluster Azure Data Explorer

Uma [identidade gerida do Azure Ative Directory](/azure/active-directory/managed-identities-azure-resources/overview) permite ao seu cluster aceder facilmente a outros recursos protegidos pela AAD, como o Azure Key Vault. A identidade é gerida pela plataforma Azure e não requer que você proílhe ou gire quaisquer segredos. Este artigo mostra-lhe como criar uma identidade gerida para clusters do Azure Data Explorer. A configuração de identidade gerida é atualmente suportada apenas para [ativar as chaves geridas pelo cliente para o seu cluster](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault).

> [!Note]
> As identidades geridas para o Azure Data Explorer não se comportarão como esperado se o seu cluster Azure Data Explorer for migrado através de subscrições ou inquilinos. A aplicação terá de obter uma nova identidade, o que pode ser feito [desativando](#disable-a-system-assigned-identity) e [reativando](#add-a-system-assigned-identity) a funcionalidade. As políticas de acesso aos recursos a jusante também terão de ser atualizadas para utilizar a nova identidade.

## <a name="add-a-system-assigned-identity"></a>Adicione uma identidade atribuída ao sistema
                                                                                                    
Atribuir uma identidade atribuída ao sistema que esteja ligada ao seu cluster e seja eliminada se o seu cluster for eliminado. Um aglomerado só pode ter uma identidade atribuída ao sistema. A criação de um cluster com uma identidade atribuída ao sistema requer que seja definida uma propriedade adicional no cluster. A identidade atribuída ao sistema é adicionada usando C#, modelos ARM ou o portal Azure como descrito abaixo.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

### <a name="add-a-system-assigned-identity-using-the-azure-portal"></a>Adicione uma identidade atribuída ao sistema utilizando o portal Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

#### <a name="new-azure-data-explorer-cluster"></a>Novo cluster do Explorador de Dados Azure

1. [Criar um cluster Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal#create-a-cluster) 
1. No separador **de segurança** > **Sistema atribuído**, selecione **On**. Para remover a identidade atribuída ao sistema, selecione **Off**.
2. Selecione **Next:Tags>** ou **Revê + criar** para criar o cluster.

    ![Adicionar sistema atribuído identidade a novo cluster](media/managed-identities/system-assigned-identity-new-cluster.png)

#### <a name="existing-azure-data-explorer-cluster"></a>Cluster existente do Explorador de Dados Azure

1. Abra um cluster azure data explorer existente.
1. Selecione **Definições** > **Identidade** no painel esquerdo do portal.
1. No separador > do painel **de identidade** **atribuído:**
   1. Mova o slider **de Estado** para **on**. .
   1. Selecione **Guardar**
   1. Na janela pop-up, selecione **Sim**

    ![Adicionar identidade atribuída ao sistema](media/managed-identities/turn-system-assigned-identity-on.png)

1. Após alguns minutos, o ecrã mostra: 
  * **ID do objeto** - usado para chaves geridas pelo cliente 
  * **Atribuição de papéis** - clique em link para atribuir funções relevantes

    ![Sistema de identidade atribuída em](media/managed-identities/system-assigned-identity-on.png)

# <a name="c"></a>[C #](#tab/c-sharp)

### <a name="add-a-system-assigned-identity-using-c"></a>Adicione uma identidade atribuída ao sistema usando C #

#### <a name="prerequisites"></a>Pré-requisitos

Para criar uma identidade gerida utilizando o cliente Azure Data Explorer C# :

* Instale o pacote NuGet do [Azure Data Explorer (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Instale o [pacote Microsoft.IdentityModel.Clients.ActiveDirectory NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) para autenticação.
* Crie um diretor de aplicação e serviço [azure AD](/azure/active-directory/develop/howto-create-service-principal-portal) que possa aceder a recursos. Adiciona-se a atribuição de funções `Directory (tenant) ID`no `Application ID`âmbito `Client Secret`da subscrição e obtém o necessário, e .

#### <a name="create-or-update-your-cluster"></a>Crie ou atualize o seu cluster

1. Crie ou atualize `Identity` o seu cluster utilizando a propriedade:

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
    var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
    var credential = new ClientCredential(clientId, clientSecret);
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);
    
    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);
    
    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = subscriptionId
    };
                                                                                                    
    var resourceGroupName = "testrg";
    var clusterName = "mykustocluster";
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var identity = new Identity(IdentityType.SystemAssigned);
    var cluster = new Cluster(location, sku, identity: identity);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
2. Execute o seguinte comando para verificar se o seu cluster foi criado com sucesso ou atualizado com uma identidade:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Se o `ProvisioningState` resultado `Succeeded` contiver o valor, então o cluster foi criado ou atualizado, e deve ter as seguintes propriedades:

    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

`PrincipalId`e `TenantId` são substituídos por GUIDs. A `TenantId` propriedade identifica o inquilino da AAD a que pertence a identidade. O `PrincipalId` é um identificador único para a nova identidade do cluster. Dentro da AAD, o diretor de serviço tem o mesmo nome que deu ao seu Serviço de Aplicações ou à instância De Funções Azure.

# <a name="arm-template"></a>[Modelo de BRAÇO](#tab/arm)

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Adicione uma identidade atribuída ao sistema usando um modelo de Gestor de Recursos Azure

Um modelo de Gestor de Recursos Azure pode ser usado para automatizar a implantação dos seus recursos Azure. Para saber mais sobre a implementação para o Azure Data Explorer, consulte [Criar um cluster e base de dados do Azure Data Explorer utilizando um modelo](create-cluster-database-resource-manager.md)de Gestor de Recursos Azure .

Adicionar o tipo designado pelo sistema diz ao Azure para criar e gerir a identidade para o seu cluster. Qualquer recurso `Microsoft.Kusto/clusters` de tipo pode ser criado com uma identidade, incluindo a seguinte propriedade na definição de recurso: 

```json
"identity": {
    "type": "SystemAssigned"
}    
```

Por exemplo:

```json
{
    "apiVersion": "2019-09-07",
    "type": "Microsoft.Kusto/clusters",
    "name": "[variables('clusterName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "trustedExternalTenants": [],
        "virtualNetworkConfiguration": null,
        "optimizedAutoscale": null,
        "enableDiskEncryption": false,
        "enableStreamingIngest": false,
    }
}
```

Quando o cluster é criado, tem as seguintes propriedades adicionais:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>`e `<PRINCIPALID>` são substituídos por GUIDs. A `TenantId` propriedade identifica o inquilino da AAD a que pertence a identidade. O `PrincipalId` é um identificador único para a nova identidade do cluster. Dentro da AAD, o diretor de serviço tem o mesmo nome que deu ao seu Serviço de Aplicações ou à instância De Funções Azure.

---

## <a name="disable-a-system-assigned-identity"></a>Desativar uma identidade atribuída ao sistema

A remoção de uma identidade atribuída ao sistema também a eliminará do AAD. As identidades atribuídas ao sistema também são automaticamente removidas do AAD quando o recurso do cluster é eliminado. Uma identidade atribuída ao sistema pode ser removida desativando a funcionalidade.  A identidade atribuída ao sistema é removida usando C#, modelos ARM ou o portal Azure conforme descrito abaixo.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

### <a name="disable-a-system-assigned-identity-using-the-azure-portal"></a>Desativar uma identidade atribuída ao sistema utilizando o portal Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
1. Selecione **Definições** > **Identidade** no painel esquerdo do portal.
1. No separador > do painel **de identidade** **atribuído:**
    1. Desloque o slider **de Estado** para **desligar**.
    1. Selecione **Guardar**
    1. Na janela pop-up, selecione **Sim** para desativar a identidade atribuída ao sistema. O painel **de identidade** reverte para a mesma condição que antes da adição da identidade atribuída ao sistema.

    ![Sistema atribuído identidade fora](media/managed-identities/system-assigned-identity.png)

# <a name="c"></a>[C #](#tab/c-sharp)

### <a name="remove-a-system-assigned-identity-using-c"></a>Remover uma identidade atribuída ao sistema usando C #

Executar o seguinte para remover a identidade atribuída ao sistema:

```csharp
var identity = new Identity(IdentityType.None);
var cluster = new Cluster(location, sku, identity: identity);
await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
```

# <a name="arm-template"></a>[Modelo de BRAÇO](#tab/arm)

### <a name="remove-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Remova uma identidade atribuída ao sistema usando um modelo de Gestor de Recursos Azure

Executar o seguinte para remover a identidade atribuída ao sistema:

```json
"identity": {
    "type": "None"
}
```

---

## <a name="next-steps"></a>Passos seguintes

* [Clusters Secure Azure Data Explorer em Azure](security.md)
* [Proteja o seu cluster no Azure Data Explorer - portal Azure,](manage-cluster-security.md) permitindo a encriptação em repouso.
 * [Configure as chaves geridas pelo cliente utilizando C #](customer-managed-keys-csharp.md)
 * [Configure as chaves geridas pelo cliente utilizando o modelo do Gestor de Recursos Azure](customer-managed-keys-resource-manager.md)
