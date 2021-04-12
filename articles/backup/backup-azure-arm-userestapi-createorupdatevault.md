---
title: Criar cofres de Serviços de Recuperação usando REST API
description: Neste artigo, aprenda a gerir backup e restaurar as operações de Backup Azure VM usando REST API.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: a37808548ec58977b7d6af16c75b94b7b5efe446
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002941"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Criar cofre dos Serviços de Recuperação Azure usando REST API

Os passos para criar um cofre dos Serviços de Recuperação Azure usando a API REST são delineados na criação de documentação [API de REPOUSO DO COFRE.](/rest/api/recoveryservices/vaults/createorupdate) Vamos usar este documento como referência para criar um cofre chamado "testVault" em "West US".

Para criar ou atualizar um cofre dos Serviços de Recuperação Azure, utilize a seguinte operação *PUT.*

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Criar um pedido

Para criar o pedido *PUT,* `{subscription-id}` o parâmetro é necessário. Se tiver várias subscrições, consulte [Trabalhar com várias subscrições](/cli/azure/manage-azure-subscriptions-azure-cli). Define um `{resourceGroupName}` e para os seus `{vaultName}` recursos, juntamente com o `api-version` parâmetro. Este artigo `api-version=2016-06-01` utiliza.

Os seguintes cabeçalhos são obrigatórios:

| Cabeçalho do pedido   | Descrição |
|------------------|-----------------|
| *Content-Type:*  | Necessário. Definido como `application/json`. |
| *Authorization:* | Necessário. Definido como um [token de acesso `Bearer` ](/rest/api/azure/#authorization-code-grant-interactive-clients) válido. |

Para obter mais informações sobre como criar o pedido, consulte [Componentes de um pedido/resposta da API REST](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Criar o corpo de pedido

As seguintes definições comuns são utilizadas para construir um organismo de pedido:

|Name  |Necessário  |Tipo  |Description  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  ETag opcional       |
|localização     |  true       |String         |   Localização do recurso      |
|propriedades     |         | [VaultProperties](/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Propriedades do cofre       |
|sku     |         |  [Sku](/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Identifica o identificador de sistema único para cada recurso Azure     |
|etiquetas     |         | Objeto        |     Etiquetas de recursos    |

Note que o nome do grupo do cofre e do grupo de recursos são fornecidos no PUT URI. O órgão de pedido define a localização.

## <a name="example-request-body"></a>Corpo de pedido de exemplo

O seguinte exemplo do corpo é usado para criar um cofre em "West US". Especifique a localização. O SKU é sempre "Standard".

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>Respostas

Existem duas respostas bem sucedidas para a operação para criar ou atualizar um cofre dos Serviços de Recuperação:

|Nome  |Tipo  |Description  |
|---------|---------|---------|
|200 OK     |   [Cofre](/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
|201 Criado     | [Cofre](/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Criado      |

Para obter mais informações sobre as respostas da API rest, consulte [processar a mensagem de resposta](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Resposta de exemplo

Uma resposta condensada *201 Criada* do organismo de pedido de exemplo anterior mostra que foi atribuído um *id* e o *Estado de provisionamento* é *bem sucedido:*

```json
{
  "location": "westus",
  "name": "testVault",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "id": "/subscriptions/77777777-b0c6-47a2-b37c-d8e65a629c18/resourceGroups/Default-RecoveryServices-ResourceGroup/providers/Microsoft.RecoveryServices/vaults/testVault",
  "type": "Microsoft.RecoveryServices/vaults",
  "sku": {
    "name": "Standard"
  }
}
```

## <a name="next-steps"></a>Passos seguintes

[Crie uma política de backup para apoiar um VM Azure neste cofre](backup-azure-arm-userestapi-createorupdatepolicy.md).

Para obter mais informações sobre as APIs do Azure REST, consulte os seguintes documentos:

- [Prestador de Serviços de Recuperação Azure REST API](/rest/api/recoveryservices/)
- [Introdução à API REST do Azure](/rest/api/azure/)
