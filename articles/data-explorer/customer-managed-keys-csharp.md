---
title: Configurar chaves gerenciadas pelo cliente usando oC#
description: Este artigo descreve como configurar a criptografia de chaves gerenciadas pelo cliente em seus dados no Azure Data Explorer.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 16c108790dd696e98a1264691254c9e99dac6cd3
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2020
ms.locfileid: "76280612"
---
# <a name="configure-customer-managed-keys-using-c"></a>Configurar chaves gerenciadas pelo cliente usando oC#

> [!div class="op_single_selector"]
> * [C#](customer-managed-keys-csharp.md)
> * [Modelo do Azure Resource Manager](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurar a criptografia com chaves gerenciadas pelo cliente

Esta seção mostra como configurar a criptografia de chaves gerenciadas pelo cliente usando o cliente C# do Azure data Explorer. 

### <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver o Visual Studio 2019 instalado, poderá baixar e usar o [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)gratuito. Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

### <a name="install-c-nuget"></a>Instalar C# o NuGet

* Instale o [pacote NuGet do Azure data Explorer (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

* Instale o [pacote NuGet Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) para autenticação.

### <a name="authentication"></a>Autenticação

Para executar os exemplos neste artigo, [crie um aplicativo do Azure AD e uma](/azure/active-directory/develop/howto-create-service-principal-portal) entidade de serviço que possa acessar recursos. Você pode adicionar a atribuição de função no escopo da assinatura e obter os `Directory (tenant) ID`, `Application ID`e `Client Secret`necessários.

### <a name="configure-cluster"></a>Configurar cluster

Por padrão, a criptografia de Data Explorer do Azure usa chaves gerenciadas pela Microsoft. Configure seu cluster de Data Explorer do Azure para usar chaves gerenciadas pelo cliente e especifique a chave a ser associada ao cluster.

1. Atualize o cluster usando o seguinte código:

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
    var keyName = "myKey";
    var keyVersion = "5b52b20e8d8a42e6bd7527211ae32654";
    var keyVaultUri = "https://mykeyvault.vault.azure.net/";
    var keyVaultProperties = new KeyVaultProperties (keyName, keyVersion, keyVaultUri);
    var clusterUpdate = new ClusterUpdate(keyVaultProperties: keyVaultProperties);
    await kustoManagementClient.Clusters.UpdateAsync(resourceGroupName, clusterName, clusterUpdate);
    ```

1. Execute o seguinte comando para verificar se o cluster foi atualizado com êxito:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Se o resultado contiver `ProvisioningState` com o valor de `Succeeded`, o cluster foi atualizado com êxito.

## <a name="update-the-key-version"></a>Atualizar a versão de chave

Ao criar uma nova versão de uma chave, você precisará atualizar o cluster para usar a nova versão. Primeiro, chame `Get-AzKeyVaultKey` para obter a versão mais recente da chave. Em seguida, atualize as propriedades do cofre de chaves do cluster para usar a nova versão da chave, conforme mostrado em [Configurar cluster](#configure-cluster).

## <a name="next-steps"></a>Passos seguintes

* [Proteger clusters de Data Explorer do Azure no Azure](security.md)
* [Configurar identidades gerenciadas para o cluster de Data Explorer do Azure](managed-identities.md)
* [Proteja seu cluster no Azure data Explorer-portal do Azure](manage-cluster-security.md) habilitando a criptografia em repouso.
* [Configurar chaves gerenciadas pelo cliente usando o modelo de Azure Resource Manager](customer-managed-keys-resource-manager.md)


