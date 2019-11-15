---
title: Esquema de evento da grade de eventos do Azure para Azure Key Vault
description: Descreve as propriedades e o esquema fornecido para eventos de Azure Key Vault com a grade de eventos do Azure
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 17404388b2b6c3fee1c6ab666f7233a66817f642
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082876"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>Esquema de evento da grade de eventos do Azure para Azure Key Vault (versão prévia)

Este artigo fornece as propriedades e o esquema para eventos no [Azure Key Vault](../key-vault/index.yml), atualmente em visualização. Para obter uma introdução aos esquemas de evento, consulte [esquema de evento da grade de eventos do Azure](event-schema.md).

## <a name="available-event-types"></a>Tipos de evento disponíveis

Uma conta de Azure Key Vault gera os seguintes tipos de evento:

| Nome completo do evento | Nome de exibição do evento | Descrição |
| ---------- | ----------- |---|
| Microsoft. keyvault. CertificateNewVersionCreated | Nova versão de certificado criada | Disparado quando um novo certificado ou nova versão do certificado é criado. |
| Microsoft. keyvault. CertificateNearExpiry | Certificado próximo à expiração | Disparado quando a versão atual do certificado está prestes a expirar. (O padrão é 30 dias antes da data de validade.) |
| Microsoft. keyvault. CertificateExpired | Certificado Expirado | Disparado quando o certificado expira. |
| Microsoft. keyvault. KeyNewVersionCreated | Nova versão de chave criada | Disparado quando uma nova versão de chave ou nova chave é criada. |
| Microsoft. keyvault. KeyNearExpiry | Chave próxima à expiração | Disparado quando a versão atual de uma chave está prestes a expirar. (O padrão é 30 dias antes da data de validade.) |
| Microsoft. keyvault. keyvenceu | Chave expirada | Disparado quando uma chave expira. |
| Microsoft. keyvault. SecretNewVersionCreated | Nova versão secreta criada | Disparado quando uma nova versão secreta ou nova secreta é criada. |
| Microsoft. keyvault. SecretNearExpiry | Segredo próximo da expiração | Disparado quando a versão atual de um segredo está prestes a expirar. (O padrão é 30 dias antes da data de validade.) |
| Microsoft. keyvault. SecretExpired | Segredo expirado | Disparado quando um segredo expira. |

## <a name="event-examples"></a>Exemplos de eventos

O exemplo a seguir mostra o esquema para **Microsoft. keyvault. SecretNewVersionCreated**:

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

## <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | Tipo | Descrição |
| ---------- | ----------- |---|
| ID | string | A ID do objeto que disparou este evento |
| vaultName | string | O nome do cofre de chaves do objeto que disparou este evento |
| objectType | string | O tipo do objeto que disparou este evento |
| ObjectName | string | O nome do objeto que disparou este evento |
| versão | string | A versão do objeto que disparou este evento |
| NBF | número | A data de não-antes, em segundos, desde 1970-01-01T00:00:00Z do objeto que disparou este evento |
| Exp | número | A data de validade em segundos desde 1970-01-01T00:00:00Z do objeto que disparou este evento |


## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução à grade de eventos do Azure, consulte [o que é a grade de eventos?](overview.md).
* Para obter mais informações sobre como criar uma assinatura da grade de eventos do Azure, consulte [esquema de assinatura da grade de eventos](subscription-creation-schema.md).
* Para saber mais sobre a integração de Key Vault com a grade de eventos, consulte [monitorando Key Vault com a grade de eventos do Azure (versão prévia)](../key-vault/event-grid-overview.md).
* Para obter um tutorial sobre a integração Key Vault com a grade de eventos, consulte [receber e responder a notificações do Key Vault com a grade de eventos do Azure (versão prévia)](../key-vault/event-grid-tutorial.md).
* Para obter diretrizes adicionais para Key Vault e a automação do Azure, consulte:
    - [O que é Azure Key Vault?](../key-vault/key-vault-overview.md)
    - [Key Vault de monitoramento com a grade de eventos do Azure (versão prévia)](../key-vault/event-grid-overview.md)
    - [Receber e responder a notificações do cofre de chaves com a grade de eventos do Azure (versão prévia)](../key-vault/event-grid-tutorial.md)
    - [Descrição geral da Automatização do Azure](../automation/index.yml)
