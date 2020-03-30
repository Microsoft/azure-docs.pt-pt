---
title: 'Configure as chaves geridas pelo cliente utilizando C #'
description: Este artigo descreve como configurar a encriptação de chaves geridas pelo cliente nos seus dados no Azure Data Explorer.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: a00b0876c4a188b932032129ed5a394e94198930
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297952"
---
# <a name="configure-customer-managed-keys-using-c"></a>Configure as chaves geridas pelo cliente utilizando C #

> [!div class="op_single_selector"]
> * [Portal](customer-managed-keys-portal.md)
> * [C #](customer-managed-keys-csharp.md)
> * [Modelo Azure Resource Manager](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](../../includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Configure encriptação com chaves geridas pelo cliente

Esta secção mostra-lhe como configurar a encriptação de chaves gerida pelo cliente utilizando o cliente Azure Data Explorer C#. 

### <a name="prerequisites"></a>Pré-requisitos

* Se não tiver o Visual Studio 2019 instalado, pode descarregar e utilizar a Edição Comunitária **gratuita** do [Visual Studio 2019.](https://www.visualstudio.com/downloads/) Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

### <a name="install-c-nuget"></a>Instalar C# NuGet

* Instale o pacote NuGet do [Azure Data Explorer (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

* Instale o [pacote Microsoft.IdentityModel.Clients.ActiveDirectory NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) para autenticação.

### <a name="authentication"></a>Autenticação

Para executar os exemplos deste artigo, crie uma aplicação e diretor de serviço [seletiva azure](/azure/active-directory/develop/howto-create-service-principal-portal) que possa aceder a recursos. Pode adicionar a atribuição de funções no `Directory (tenant) ID` `Application ID`âmbito `Client Secret`da subscrição e obter o necessário, e .

### <a name="configure-cluster"></a>Configurar cluster

Por padrão, a encriptação do Azure Data Explorer utiliza chaves geridas pela Microsoft. Configure o seu cluster Azure Data Explorer para utilizar chaves geridas pelo cliente e especificar a chave para associar-se ao cluster.

1. Atualize o seu cluster utilizando o seguinte código:

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

1. Execute o seguinte comando para verificar se o seu cluster foi atualizado com sucesso:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Se o `ProvisioningState` resultado `Succeeded` contiver o valor, então o seu cluster foi atualizado com sucesso.

## <a name="update-the-key-version"></a>Atualizar a versão chave

Quando criar uma nova versão de uma chave, terá de atualizar o cluster para utilizar a nova versão. Primeiro, `Get-AzKeyVaultKey` ligue para obter a versão mais recente da chave. Em seguida, atualize as propriedades chave do cofre do cluster para utilizar a nova versão da chave, como mostrado no [cluster Configure](#configure-cluster).

## <a name="next-steps"></a>Passos seguintes

* [Clusters Secure Azure Data Explorer em Azure](security.md)
* [Configure identidades geridas para o seu cluster Azure Data Explorer](managed-identities.md)
* [Proteja o seu cluster no Azure Data Explorer - portal Azure,](manage-cluster-security.md) permitindo a encriptação em repouso.
* [Configure as chaves geridas pelo cliente utilizando o modelo do Gestor de Recursos Azure](customer-managed-keys-resource-manager.md)


