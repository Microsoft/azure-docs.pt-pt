---
title: Criar cofres de serviços de recuperação usando a API REST
description: Neste artigo, aprenda a gerir as operações de backup e restauro de backup seletiva do Azure VM utilizando a REST API.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 1901c35d2b4d8bcd02cc064fcfc844e19969e3b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173407"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Create Azure Recovery Services Vault usando rest API

As etapas para criar um Cofre de Serviços de Recuperação Azure utilizando a API REST são descritas na criação de documentação [abóbada REST API.](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate) Usemos este documento como referência para criar um cofre chamado "testVault" em "West US".

Para criar ou atualizar um cofre dos Serviços de Recuperação Azure, utilize a seguinte operação *PUT.*

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Criar um pedido

Para criar o pedido `{subscription-id}` *PUT,* é necessário o parâmetro. Se tiver várias subscrições, consulte [Trabalhar com várias subscrições.](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest) Define um `{resourceGroupName}` `{vaultName}` e para os seus `api-version` recursos, juntamente com o parâmetro. Este artigo `api-version=2016-06-01`usa.

Os seguintes cabeçalhos são obrigatórios:

| Cabeçalho do pedido   | Descrição |
|------------------|-----------------|
| *Tipo de conteúdo:*  | Necessário. Definido como `application/json`. |
| *Authorization:* | Necessário. Definido como um  [token de acesso `Bearer`](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) válido. |

Para obter mais informações sobre como criar o pedido, consulte [Componentes de um pedido/resposta REST API](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Criar o corpo de pedido

As seguintes definições comuns são utilizadas para construir um organismo de pedido:

|Nome  |Necessário  |Tipo  |Descrição  |
|---------|---------|---------|---------|
|eTag     |         |   Cadeia      |  ETag opcional       |
|localização     |  true       |Cadeia         |   Localização do recurso      |
|propriedades     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Propriedades do cofre       |
|sku     |         |  [Rio Sku](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Identifica o identificador de sistema único para cada recurso Azure     |
|etiquetas     |         | Objeto        |     Etiquetas de recursos    |

Note que o nome do cofre e o nome do grupo de recursos são fornecidos no PUT URI. O órgão de pedido define a localização.

## <a name="example-request-body"></a>Corpo de pedido de exemplo

O corpo de exemplo que se segue é usado para criar um cofre em "West US". Especifique a localização. O SKU é sempre "Standard".

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

Existem duas respostas bem sucedidas para a operação para criar ou atualizar um cofre de Serviços de Recuperação:

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     |   [Cofre](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
|201 Criado     | [Cofre](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Criado      |

Para obter mais informações sobre as respostas da API REST, consulte [Processe a mensagem de resposta](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Resposta de exemplo

Uma resposta condensada *de 201 Criada* pelo organismo de pedido de exemplo anterior mostra que foi atribuído um *id* e o Estado de *provisionamento* é *bem sucedido:*

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

[Crie uma política de backup para apoiar um VM Azure neste cofre.](backup-azure-arm-userestapi-createorupdatepolicy.md)

Para obter mais informações sobre as APIs do Azure REST, consulte os seguintes documentos:

- [Prestador de serviços de recuperação azure REST API](/rest/api/recoveryservices/)
- [Introdução à API REST do Azure](/rest/api/azure/)
