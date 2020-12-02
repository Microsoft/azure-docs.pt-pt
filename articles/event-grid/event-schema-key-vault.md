---
title: Cofre de Chaves Azure como fonte de grade de eventos
description: Descreve as propriedades e esquemas fornecidos para eventos Azure Key Vault com Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 605502794f2f3aa4f4edd14b49efda5003b91146
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460406"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Cofre de Chaves Azure como fonte de grade de eventos

Este artigo fornece as propriedades e esquema para eventos em [Azure Key Vault](../key-vault/index.yml). Para uma introdução aos esquemas de eventos, consulte [o esquema do evento Azure Event Grid](event-schema.md).

## <a name="event-grid-event-schema"></a>Esquema de eventos do Event Grid

### <a name="available-event-types"></a>Tipos de eventos disponíveis

Uma conta Azure Key Vault gera os seguintes tipos de eventos:

| Nome completo do evento | Nome de exibição do evento | Descrição |
| ---------- | ----------- |---|
| Microsoft.KeyVault.CertificateNewVersionCreated | Certificado nova versão criada | Desencadeado quando um novo certificado ou nova versão de certificado é criado. |
| Microsoft.KeyVault.CertificateNearExpiry | Certificado perto de expiração | Desencadeado quando a versão atual do certificado está prestes a expirar. (O evento é desencadeado 30 dias antes da data de validade.) |
| Microsoft.KeyVault.CertificateExpired | Certificado Expirado | Desencadeado quando o certificado expirar. |
| Microsoft.KeyVault.KeyNewVersionCreated | Nova versão chave criada | Desencadeado quando uma nova chave ou nova versão chave é criada. |
| Microsoft.KeyVault.KeyNearExpiry | Chave perto da expiração | Desencadeado quando a versão atual de uma chave está prestes a expirar. (O evento é desencadeado 30 dias antes da data de validade.) |
| Microsoft.KeyVault.KeyExpired | Chave expirada | Acionado quando uma chave expira. |
| Microsoft.KeyVault.SecretNewVersionCreated | Nova versão secreta criada | Desencadeado quando uma nova versão secreta ou secreta é criada. |
| Microsoft.KeyVault.SecretNearExpiry | Segredo perto de expiração | Desencadeado quando a versão atual de um segredo está prestes a expirar. (O evento é desencadeado 30 dias antes da data de validade.) |
| Microsoft.KeyVault.SecretExpired | Segredo expirado | Desencadeado quando um segredo expira. |
| Microsoft.KeyVault.VaultAccessPolicyChanged | Política de acesso ao cofre alterada | Desencadeado quando uma política de acesso no Key Vault mudou. Inclui um cenário quando o modelo de permissão do Key Vault é alterado de/para Azure RBAC  |

### <a name="event-examples"></a>Exemplos de eventos

O seguinte exemplo mostra esquema para **Microsoft.KeyVault.SecretNewVersionCreated**:

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

### <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de alto nível:

| Propriedade | Tipo | Descrição |
| ---------- | ----------- |---|
| ID | string | A ID do objeto que desencadeou este evento |
| nome do cofre | string | O nome chave do cofre do objeto que desencadeou este evento |
| objectType | string | O tipo de objeto que desencadeou este evento |
| objetoName | string | O nome do objeto que desencadeou este evento |
| versão | string | A versão do objeto que desencadeou este evento |
| nbf | número | A data não antes em segundos desde 1970-01-01T00:00:00Z do objeto que desencadeou este evento |
| exp | número | A data de validade em segundos desde 1970-01-01T00:00:00Z do objeto que desencadeou este evento |

## <a name="tutorials-and-how-tos"></a>Tutorials and how-tos (Tutoriais e procedimentos)
|Título  |Descrição  |
|---------|---------|
| [Monitorização de eventos do Cofre de Chaves com Azure Event Grid](../key-vault/general/event-grid-overview.md) | Visão geral da integração do Cofre de Chaves com Grade de Eventos. |
| [Tutorial: Criar e monitorizar eventos do Cofre de Chaves com Grelha de Eventos](../key-vault/general/event-grid-logicapps.md) | Saiba como configurar notificações de Grade de Eventos para Key Vault. |


## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grade de Eventos?](overview.md)
* Para obter mais informações sobre como criar uma subscrição da Azure Event Grid, consulte [o esquema de subscrição da Grade de Eventos.](subscription-creation-schema.md)
* Para mais informações sobre o Key Vault, veja [o que é o Cofre da Chave Azure?](../key-vault/general/overview.md)

