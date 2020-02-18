---
title: Como configurar identidades geridas para o cluster Azure Data Explorer
description: Saiba como configurar identidades geridas para o cluster Do Explorador de Dados do Azure.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: e76ae2e072bb780ac9788902e9157db871e4f09d
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2020
ms.locfileid: "77373371"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Configure identidades geridas para o seu cluster Azure Data Explorer

Uma [identidade gerida do Azure Ative Directory](/azure/active-directory/managed-identities-azure-resources/overview) permite ao seu cluster aceder facilmente a outros recursos protegidos pela AAD, como o Azure Key Vault. A identidade é gerida pela plataforma Azure e não requer que você proílhe ou gire quaisquer segredos. Este artigo mostra-lhe como criar uma identidade gerida para clusters do Azure Data Explorer. A configuração de identidade gerida é atualmente suportada apenas para [ativar as chaves geridas pelo cliente para o seu cluster](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault).

> [!Note]
> As identidades geridas para o Azure Data Explorer não se comportarão como esperado se a sua aplicação for migrada para subscrições ou inquilinos. A aplicação terá de obter uma nova identidade, o que pode ser feito desativando e reativando a funcionalidade utilizando [a remoção de uma identidade](#remove-an-identity). As políticas de acesso aos recursos a jusante também terão de ser atualizadas para utilizar a nova identidade.

## <a name="add-a-system-assigned-identity"></a>Adicione uma identidade atribuída ao sistema

O seu cluster pode ser atribuído a uma **identidade atribuída** ao sistema que está ligada ao seu cluster, e é eliminada se o seu cluster for eliminado. Um aglomerado só pode ter uma identidade atribuída ao sistema. A criação de um cluster com uma identidade atribuída ao sistema requer que seja definida uma propriedade adicional no cluster.

### <a name="add-a-system-assigned-identity-using-c"></a>Adicione uma identidade atribuída ao sistema usandoC#

Para criar uma identidade gerida utilizando o C# cliente Do Explorador de Dados Azure, faça o seguinte:

* Instale o pacote NuGet do [Azure Data Explorer (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Instale o [pacote Microsoft.IdentityModel.Clients.ActiveDirectory NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) para autenticação.
* Para executar o seguinte exemplo, [crie uma aplicação](/azure/active-directory/develop/howto-create-service-principal-portal) e um diretor de serviço da Azure AD que possam aceder aos recursos. Pode adicionar a atribuição de funções no âmbito da subscrição e obter o `Directory (tenant) ID`necessário, `Application ID`e `Client Secret`.

#### <a name="create-or-update-your-cluster"></a>Crie ou atualize o seu cluster

1. Crie ou atualize o seu cluster utilizando a propriedade `Identity`:

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

    Se o resultado contiver `ProvisioningState` com o valor `Succeeded`, então o cluster foi criado ou atualizado, e deve ter as seguintes propriedades:
   
    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

    `PrincipalId` e `TenantId` são substituídos por GUIDs. A propriedade `TenantId` identifica o inquilino da AAD a que pertence a identidade. O `PrincipalId` é um identificador único para a nova identidade do cluster. Dentro da AAD, o diretor de serviço tem o mesmo nome que deu ao seu Serviço de Aplicações ou à instância De Funções Azure.

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Adicione uma identidade atribuída ao sistema usando um modelo de Gestor de Recursos Azure

Um modelo de Gestor de Recursos Azure pode ser usado para automatizar a implantação dos seus recursos Azure. Para saber mais sobre a implementação para o Azure Data Explorer, consulte [Criar um cluster e base de dados do Azure Data Explorer utilizando um modelo](create-cluster-database-resource-manager.md)de Gestor de Recursos Azure .

Adicionar o tipo designado pelo sistema diz ao Azure para criar e gerir a identidade para o seu cluster. Qualquer recurso de tipo `Microsoft.Kusto/clusters` pode ser criado com uma identidade, incluindo a seguinte propriedade na definição de recursos: 

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

`<TENANTID>` e `<PRINCIPALID>` são substituídos por GUIDs. A propriedade `TenantId` identifica o inquilino da AAD a que pertence a identidade. O `PrincipalId` é um identificador único para a nova identidade do cluster. Dentro da AAD, o diretor de serviço tem o mesmo nome que deu ao seu Serviço de Aplicações ou à instância De Funções Azure.

## <a name="remove-an-identity"></a>Remover uma identidade

A remoção de uma identidade atribuída ao sistema também a eliminará do AAD. As identidades atribuídas ao sistema também são automaticamente removidas do AAD quando o recurso do cluster é eliminado. Uma identidade atribuída ao sistema pode ser removida desativando a funcionalidade:

```json
"identity": {
    "type": "None"
}
```

## <a name="next-steps"></a>Passos seguintes

* [Clusters Secure Azure Data Explorer em Azure](security.md)
* [Proteja o seu cluster no Azure Data Explorer - portal Azure,](manage-cluster-security.md) permitindo a encriptação em repouso.
 * [Configure as chaves geridas pelo cliente usandoC#](customer-managed-keys-csharp.md)
 * [Configure as chaves geridas pelo cliente utilizando o modelo do Gestor de Recursos Azure](customer-managed-keys-resource-manager.md)
