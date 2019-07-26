---
title: 'Backup do Azure: Criar cofres dos serviços de recuperação usando a API REST'
description: gerenciar operações de backup e restauração do backup de VM do Azure usando a API REST
author: pvrk
manager: shivamg
keywords: API REST; Backup de VM do Azure; Restauração de VM do Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: pullabhk
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 0373098dd344df79be79871227f20c8a995958fa
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466946"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Criar um cofre dos serviços de recuperação do Azure usando a API REST

As etapas para criar um cofre dos serviços de recuperação do Azure usando a API REST são descritas na documentação [criar a API REST do cofre](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate) . Deixe-nos usar este documento como uma referência para criar um cofre chamado "testVault" em "oeste dos EUA".

Para criar ou atualizar um cofre dos serviços de recuperação do Azure, use a seguinte operação *Put* .

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Criar um pedido

Para criar a solicitação *Put* , o `{subscription-id}` parâmetro é necessário. Se você tiver várias assinaturas, consulte [trabalhando com várias assinaturas](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). Você define um `{resourceGroupName}` e `{vaultName}` para seus recursos, juntamente com o `api-version` parâmetro. Este artigo usa `api-version=2016-06-01`.

Os seguintes cabeçalhos são necessários:

| Cabeçalho da solicitação   | Descrição |
|------------------|-----------------|
| *Tipo de conteúdo:*  | Necessário. Defina como `application/json`. |
| *Authorization:* | Necessário. Defina como um token `Bearer` de [acesso](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients)válido. |

Para obter mais informações sobre como criar a solicitação, consulte [componentes de uma solicitação/resposta da API REST](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Criar o corpo da solicitação

As seguintes definições comuns são usadas para criar um corpo de solicitação:

|Nome  |Requerido  |Tipo  |Descrição  |
|---------|---------|---------|---------|
|eTag     |         |   Cadeia      |  ETag opcional       |
|location     |  true       |Cadeia         |   Localização do recurso      |
|properties     |         | [Cofreproperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Propriedades do cofre       |
|sku     |         |  [Sku](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Identifica o identificador de sistema exclusivo para cada recurso do Azure     |
|tags     |         | Objeto        |     Etiquetas de recursos    |

Observe que o nome do cofre e o nome do grupo de recursos são fornecidos no URI PUT. O corpo da solicitação define o local.

## <a name="example-request-body"></a>Corpo da solicitação de exemplo

O corpo de exemplo a seguir é usado para criar um cofre no "oeste dos EUA". Especifique o local. A SKU é sempre "padrão".

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>Responses

Há duas respostas bem-sucedidas para a operação criar ou atualizar um cofre dos serviços de recuperação:

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     |   [Cofre](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
|201 criado     | [Cofre](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Criado      |

Para obter mais informações sobre as respostas da API REST, consulte [processar a mensagem de resposta](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Resposta de exemplo

Uma resposta condensada *201 criada* no corpo da solicitação de exemplo anterior mostra que uma *ID* foi atribuída e o *provisioningState* foi *bem-sucedido*:

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

## <a name="next-steps"></a>Passos Seguintes

[Crie uma política de backup para fazer backup de uma VM do Azure neste cofre](backup-azure-arm-userestapi-createorupdatepolicy.md).

Para obter mais informações sobre as APIs REST do Azure, consulte os seguintes documentos:

- [API REST do provedor de serviços de recuperação do Azure](/rest/api/recoveryservices/)
- [Introdução à API REST do Azure](/rest/api/azure/)
