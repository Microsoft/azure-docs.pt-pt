---
title: Grade de eventos do Azure Azure Key Vault esquema de evento
description: Descreve as propriedades e o esquema fornecido para eventos de Azure Key Vault com a grade de eventos do Azure
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: c92352dd28b870c5f58dec0b82a8000f14a8e62d
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033527"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>Esquema de evento da grade de eventos do Azure para Azure Key Vault (versão prévia)

Este artigo fornece as propriedades e o esquema para [Azure Key Vault](../key-vault/index.yml) eventos, atualmente em visualização. Para obter uma introdução aos esquemas de evento, consulte [esquema de evento da grade de eventos do Azure](event-schema.md).

## <a name="available-event-types"></a>Tipos de evento disponíveis

Uma conta de Azure Key Vault emite os seguintes tipos de evento:

| Nome completo do evento | Nome de exibição do evento | descrição |
| ---------- | ----------- |---|
| Microsoft. keyvault. CertificateNewVersionCreated | Nova versão de certificado criada | Disparado quando novo certificado ou nova versão do certificado é criado |
| Microsoft. keyvault. CertificateNearExpiry | Certificado próximo à expiração | Disparado quando a versão atual do certificado está prestes a expirar (o padrão é 30 dias antes da data de vencimento) |
| Microsoft. keyvault. CertificateExpired | Certificado Expirado | Disparado quando o certificado está expirado |
| Microsoft. keyvault. KeyNewVersionCreated | Nova versão de chave criada | Acionado quando uma nova versão de chave ou nova chave é criada |
| Microsoft. keyvault. KeyNearExpiry | Chave próxima à expiração | Disparado quando a versão atual da chave está prestes a expirar (o padrão é 30 dias antes da data de vencimento) |
| Microsoft. keyvault. keyvenceu | Chave expirada | Disparado quando a chave está expirada |
| Microsoft. keyvault. SecretNewVersionCreated | Nova versão secreta criada | Disparado quando novo segredo ou nova versão secreta é criada |
| Microsoft. keyvault. SecretNearExpiry | Segredo próximo da expiração | Disparado quando a versão atual do segredo está prestes a expirar (o padrão é 30 dias antes da data de vencimento) |
| Microsoft. keyvault. SecretExpired | Segredo expirado | Disparado quando o segredo está expirado |

## <a name="event-examples"></a>Exemplos de eventos

O exemplo a seguir mostra o esquema para **Microsoft. keyvault. SecretNewVersionCreated**.

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
| ID | string | A ID do objeto que disparou este evento. |
| vaultName | string | Nome do cofre de chaves do objeto que disparou este evento. |
| objectType | string | O tipo do objeto que disparou este evento |
| ObjectName | string | O nome do objeto que disparou este evento |
| versão | string | A versão do objeto que disparou este evento |
| NBF | número | Não antes da data em segundos desde 1970-01-01T00:00:00Z do objeto que disparou este evento |
| Exp | número | A data de validade em segundos desde 1970-01-01T00:00:00Z do objeto que disparou este evento |


## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução à grade de eventos do Azure, consulte [o que é a grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da grade de eventos do Azure, consulte [esquema de assinatura da grade de eventos](subscription-creation-schema.md).
* Para saber mais sobre a integração da grade de Key Vault/eventos, consulte [Key Vault de monitoramento com a grade de eventos do Azure (versão prévia)](../key-vault/event-grid-overview.md)
* Para ver um tutorial sobre a integração da grade de eventos/Key Vault, consulte [como rotear eventos de Key Vault para o runbook de automação (versão prévia)](../key-vault/event-grid-tutorial.md).

- [Visão geral de Azure Key Vault](../key-vault/key-vault-overview.md)
- [Visão geral da grade de eventos do Azure](overview.md)
- [Key Vault de monitoramento com a grade de eventos do Azure (versão prévia)](../key-vault/event-grid-overview.md)
- [Como: rotear eventos de Key Vault para o runbook de automação (visualização)](../key-vault/event-grid-tutorial.md).
- [Descrição geral da Automatização do Azure](../automation/index.yml)
