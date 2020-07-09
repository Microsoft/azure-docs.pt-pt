---
title: Cofre de Chaves Azure como fonte de grade de eventos
description: Descreve as propriedades e esquemas fornecidos para eventos Azure Key Vault com Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 1f9cbe85de9423484343e4054be8d2d58c6c5e7e
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86109438"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Cofre de Chaves Azure como fonte de grade de eventos

Este artigo fornece as propriedades e esquema para eventos em [Azure Key Vault,](../key-vault/index.yml)atualmente em pré-visualização. Para uma introdução aos esquemas de eventos, consulte [o esquema do evento Azure Event Grid](event-schema.md).

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

| Propriedade | Tipo | Description |
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
| [Tutorial: Criar e monitorizar eventos do Cofre de Chaves com Grelha de Eventos](../key-vault/general/event-grid-tutorial.md) | Saiba como configurar notificações de Grade de Eventos para Key Vault. |


## <a name="next-steps"></a>Próximos passos

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grade de Eventos?](overview.md)
* Para obter mais informações sobre como criar uma subscrição da Azure Event Grid, consulte [o esquema de subscrição da Grade de Eventos.](subscription-creation-schema.md)
* Para saber mais sobre a integração do Key Vault com a Grade de Eventos, consulte [monitoring Key Vault com Azure Event Grid (pré-visualização)](../key-vault/general/event-grid-overview.md).
* Para um tutorial sobre a integração do Key Vault com a Grade de [Eventos, consulte receber e responder às notificações do cofre chave com a Grelha de Eventos Azure (pré-visualização)](../key-vault/general/event-grid-tutorial.md).
* Para obter orientações adicionais para o Key Vault e a Azure Automation, consulte:
    - [O que é o cofre de chave do Azure?](../key-vault/general/overview.md)
    - [Monitore cofre de chaves com grade de evento azure (pré-visualização)](../key-vault/general/event-grid-overview.md)
    - [Receber e responder às notificações do cofre chave com a Azure Event Grid (pré-visualização)](../key-vault/general/event-grid-tutorial.md)
    - [Visão geral da Azure Automation](../automation/index.yml)
