---
title: 'Cópia de segurança do Azure: Criar cofres de serviços de recuperação com a REST API'
description: gerir cópias de segurança e restaurar as operações do Azure VM Backup com a REST API
services: backup
author: pvrk
manager: shivamg
keywords: API DE REST; Cópia de segurança VM do Azure; Restauro de VMS do Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: pullabhk
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 4f18b10ee3f4148badc8e53a9660c9f5c998aef7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60646712"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Criar cofre dos serviços de recuperação do Azure com a REST API

Os passos para criar um cofre de serviços de recuperação do Azure com a REST API descritos na [criar REST API do cofre](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate) documentação. Diga-nos Utilize este documento como uma referência para criar um cofre chamado "testVault" em "E.U.A. Oeste".

Para criar ou atualizar um cofre dos serviços de recuperação do Azure, utilize o seguinte procedimento *colocar* operação.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Criar um pedido

Para criar o *colocar* pedido, o `{subscription-id}` é necessário o parâmetro. Se tiver várias subscrições, veja [trabalhar com várias subscrições](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). Define um `{resourceGroupName}` e `{vaultName}` para os seus recursos, juntamente com o `api-version` parâmetro. Este artigo utiliza `api-version=2016-06-01`.

Os seguintes cabeçalhos são necessários:

| Cabeçalho do pedido   | Descrição |
|------------------|-----------------|
| *Content-Type:*  | Necessário. Definido como `application/json`. |
| *Autorização:* | Necessário. Definido como válido `Bearer` [token de acesso](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

Para obter mais informações sobre como criar o pedido, consulte [componentes de uma REST API de solicitação/resposta](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Criar o corpo do pedido

As seguintes definições comuns são utilizadas para criar um corpo de pedido:

|Name  |Necessário  |Tipo  |Descrição  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  ETag opcional       |
|localização     |  true       |String         |   Localização do recurso      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Propriedades do Cofre       |
|sku     |         |  [Sku](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Identifica o identificador de sistema exclusivo de cada recurso do Azure     |
|etiquetas     |         | Object        |     Etiquetas de recursos    |

Tenha em atenção que o nome do cofre e o nome do grupo de recursos são fornecidos no URI de PUT. O corpo do pedido define o local.

## <a name="example-request-body"></a>Corpo do pedido de exemplo

O corpo de exemplo seguinte é utilizado para criar um cofre em "E.U.A. Oeste". Especifique a localização. O SKU é sempre "Standard".

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

Existem duas respostas com êxito para a operação criar ou atualizar um cofre dos serviços de recuperação:

|Name  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     |   [Cofre](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
|201 criado     | [Cofre](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Criado      |

Para obter mais informações sobre as respostas de REST API, consulte [processar a mensagem de resposta](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Resposta de exemplo

Um condensada *201 criado* corpo de resposta do pedido de exemplo anterior mostra um *id* foi atribuído e o *provisioningState* é *com êxito* :

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

[Criar uma política de cópia de segurança para fazer backup de uma VM do Azure neste cofre](backup-azure-arm-userestapi-createorupdatepolicy.md).

Para obter mais informações sobre as APIs de REST do Azure, consulte os seguintes documentos:

- [API de REST do fornecedor de serviços de recuperação do Azure](/rest/api/recoveryservices/)
- [Introdução à API REST do Azure](/rest/api/azure/)
