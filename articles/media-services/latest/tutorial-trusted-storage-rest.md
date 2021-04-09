---
title: Azure Media Services Trust Storage
description: Neste tutorial, você vai aprender como permitir armazenamento fidedignos para Azure Media Services, usar identidades Manged para armazenamento confiável, e dar serviços Azure para aceder a uma conta de armazenamento ao usar uma firewall ou VPN.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 2/8/2021
ms.openlocfilehash: 18cb4e3ada94822c2f4cb1ca7675310a37e44e84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100590657"
---
# <a name="tutorial-media-services-trusted-storage"></a>Tutorial: Armazenamento fidedigno dos Media Services

Neste tutorial, ficará a saber:

> [!div class="checklist"]
> - Como permitir o armazenamento fidedigno para a Azure Media Services
> - Como utilizar identidades geridas para armazenamento fidedigno
> - Como dar aos Serviços Azure o acesso a uma conta de armazenamento ao utilizar o controlo de acesso à rede, como uma firewall ou VPN

Com a API 2020-05-01, pode ativar o armazenamento fidedigno associando uma Identidade Gerida a uma conta de Serviços de Comunicação.

>[!NOTE]
>O armazenamento fidedigno só está disponível na API, e não está atualmente ativado no portal Azure.

Os Serviços de Mídia podem aceder automaticamente à sua conta de armazenamento utilizando a autenticação do sistema. Os Serviços de Comunicação Social validam que a conta de Serviços de Comunicação Social e a conta de armazenamento estão na mesma subscrição. Também valida que o utilizador que adiciona a associação tenha acesso à conta de armazenamento com o Azure Resource Manager RBAC.

No entanto, se pretender utilizar o controlo de acesso à rede para proteger a sua conta de armazenamento e permitir o armazenamento fidedigno, é necessária a autenticação [de Identidades Geridas.](concept-managed-identities.md) Permite que os Serviços de Comunicação Social acedam à conta de armazenamento que foi configurada com uma firewall ou uma restrição VNet através de acesso de armazenamento fidedigno.

## <a name="overview"></a>Descrição Geral

> [!IMPORTANT]
> Utilize a API 2020-05-01 para todos os pedidos aos Serviços de Comunicação Social.

Estes são os passos gerais para a criação de armazenamento fidedigno para os Serviços de Media:

1. Crie um grupo de recursos.
1. Criar uma conta de armazenamento.
1. Investigue a conta de armazenamento até estar pronta. Quando a conta de armazenamento estiver pronta, o pedido devolverá o ID principal do serviço.
1. Encontre o ID da função *de contribuinte de dados de armazenamento blob.*
1. Ligue para o fornecedor de autorização e adicione uma atribuição de funções.
1. Atualize a conta de serviços de comunicação para autenticar na conta de armazenamento utilizando identidade gerida.
1. Elimine os recursos se não quiser continuar a usá-los e ser cobrado por eles.

<!-- Link to storage role contributor role access differences information -->

## <a name="prerequisites"></a>Pré-requisitos

Precisa de uma assinatura Azure para começar.  Se não tiver uma subscrição do Azure, [crie uma conta de teste gratuita](https://azure.microsoft.com/free/).

### <a name="get-your-tenant-id-and-subscription-id"></a>Obtenha o seu ID de inquilino e iD de assinatura

Se não sabe como obter a identificação do seu inquilino e identificação de assinatura, consulte [como encontrar o seu ID do inquilino](how-to-set-azure-tenant.md) e encontre a [identificação do seu inquilino.](how-to-set-azure-tenant.md)

### <a name="create-a-service-principal-and-secret"></a>Criar um diretor de serviço e segredo

Se não sabe como criar um principal de serviço e segredo, consulte [obter credenciais para aceder à API dos Serviços de Comunicação](access-api-howto.md)Social.

## <a name="use-a-rest-client"></a>Use um cliente REST

Este script destina-se a ser utilizado com um cliente REST, como o que está disponível nas extensões de código do Estúdio Visual.  Adapte-o para o seu ambiente de desenvolvimento.

## <a name="set-initial-variables"></a>Definir variáveis iniciais

Esta parte do script é para uso num cliente REST. Pode utilizar variáveis de forma diferente dentro do seu ambiente de desenvolvimento.

```rest
### AAD details
@tenantId = your tenant ID
@servicePrincipalId = the service principal ID
@servicePrincipalSecret = the service principal secret

### AAD resources
@armResource = https%3A%2F%2Fmanagement.core.windows.net%2F
@graphResource = https%3A%2F%2Fgraph.windows.net%2F
@storageResource = https%3A%2F%2Fstorage.azure.com%2F

### Service endpoints
@armEndpoint = management.azure.com
@graphEndpoint = graph.windows.net
@aadEndpoint = login.microsoftonline.com

### ARM details
@subscription = your subscription id
@resourceGroup = the resource group you'll be creating
@storageName = the name of the storage you'll be creating
@accountName = the name of the account you'll be creating
@resourceLocation = East US (or the location that works best for your region)
```

## <a name="get-a-token-for-azure-resource-manager"></a>Obtenha um símbolo para O Gestor de Recursos Azure

```rest
// @name getArmToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{armResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-a-token-for-the-graph-api"></a>Obtenha um símbolo para a API do gráfico

Esta parte do script é para uso num cliente REST. Pode utilizar variáveis de forma diferente dentro do seu ambiente de desenvolvimento.

```rest
// @name getGraphToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{graphResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-the-service-principal-details"></a>Obtenha os principais detalhes do serviço

```rest
// @name getServicePrincipals
GET https://{{graphEndpoint}}/{{tenantId}}/servicePrincipals?$filter=appId%20eq%20'{{servicePrincipalId}}'&api-version=1.6
x-ms-client-request-id: cae3e4f7-17a0-476a-a05a-0dab934ba959
Authorization:  Bearer {{getGraphToken.response.body.access_token}}
```

## <a name="store-the-service-principal-id"></a>Guarde o iD principal do serviço

```rest
@servicePrincipalObjectId = {{getServicePrincipals.response.body.value[0].objectId}}
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

```rest
// @name createResourceGroup
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}
    ?api-version=2016-09-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "location": "{{resourceLocation}}"
}
```

## <a name="create-storage-account"></a>Criar conta de armazenamento

```rest
// @name createStorageAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
    }
}
```

## <a name="get-the-storage-account-status"></a>Obtenha o estado da conta de armazenamento

A conta de armazenamento levará algum tempo a estar pronta para que este pedido de sondagens para o seu estado.  Repita este pedido até que a conta de armazenamento esteja pronta.

```rest
// @name getStorageAccountStatus
GET {{createStorageAccount.response.headers.Location}}
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-the-storage-account-details"></a>Obtenha os detalhes da conta de armazenamento

Quando a conta de armazenamento estiver pronta, obtenha as propriedades da conta de armazenamento.

```rest
// @name getStorageAccount
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-a-token-for-arm"></a>Obter um símbolo para a ARM

```rest
// @name getStorageToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{storageResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="create-a-media-services-account-with-a-system-assigned-managed-identity"></a>Criar uma conta de Serviços de Mídia com uma identidade gerida atribuída ao sistema

```rest
// @name createMediaServicesAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="get-the-storage-storage-blob-data-role-definition"></a>Obtenha a definição de função de função de armazenamento Blob Data

```rest
// @name getStorageBlobDataContributorRoleDefinition
GET https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions?$filter=roleName%20eq%20%27Storage%20Blob%20Data%20Contributor%27&api-version=2015-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

### <a name="set-the-storage-role-assignment"></a>Definir a atribuição de função de armazenamento

A função diz que o principal do serviço para a conta de Serviços de Mídia tem a função de *armazenamento Storage Blob Data Contributor*.  Isto pode demorar algum tempo e é importante esperar ou a conta dos Serviços de Comunicação Social não será configurada corretamente.

```rest
PUT https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleAssignments/{{$guid}}?api-version=2020-04-01-preview
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "roleDefinitionId": "/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions/{{getStorageBlobDataContributorRoleDefinition.response.body.value[0].name}}",
    "principalId": "{{createMediaServicesAccount.response.body.identity.principalId}}"
  }
}
```

## <a name="give-managed-identity-bypass-access-to-the-storage-account"></a>Dar acesso de bypass de identidade gerida à conta de armazenamento

Esta ação altera o acesso da identidade gerida pelo sistema à Identidade Gerida. Desta forma, a conta de armazenamento pode aceder à conta de armazenamento através de uma firewall, uma vez que os serviços Azure podem aceder à conta de armazenamento independentemente das regras de acesso IP (ACLs).

Mais uma vez, aguarde até que a função seja atribuída na conta de armazenamento, ou a conta dos Serviços de Comunicação Social será configurada incorretamente.

```rest
// @name setStorageAccountFirewall
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
      "minimumTlsVersion": "TLS1_2",
      "networkAcls": {
        "bypass": "AzureServices",
        "virtualNetworkRules": [],
        "ipRules": [],
        "defaultAction": "Deny"
      }
    }
}
```

## <a name="update-the-media-services-account-to-use-the-managed-identity"></a>Atualizar a conta dos Serviços de Comunicação social para utilizar a Identidade Gerida

Este pedido pode ter de ser novamente julgado algumas vezes, uma vez que a atribuição da função de armazenamento pode demorar alguns minutos a propagar-se.

```rest
// @name updateMediaServicesAccountWithManagedStorageAuth
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "storageAuthentication": "ManagedIdentity",
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="test-access"></a>Acesso ao teste

Teste o acesso criando um ativo na conta de armazenamento.

```rest
// @name createAsset
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}/assets/testasset{{index}}withoutmi?api-version=2018-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
}
```

## <a name="delete-resources"></a>Eliminar recursos

Se não quiser manter os recursos que criou e continuar a ser cobrado por eles, elimine-os.

```rest
### Clean up the Storage account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="next-steps"></a>Passos seguintes

[Como criar um Ativo](how-to-create-asset.md)
