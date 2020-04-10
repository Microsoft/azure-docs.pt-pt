---
title: Esquema de evento de evento azure para cofre chave Azure
description: Descreve as propriedades e esquemas fornecidos para eventos azure key vault com azure event grid
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: fe186e2ba8f3cafeb4d186066ba65ae036302f70
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010516"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>Esquema de evento sinuoso do evento Azure Event Grid para cofre de chaves Azure (pré-visualização)

Este artigo fornece as propriedades e esquemas para eventos em [Azure Key Vault,](../key-vault/index.yml)atualmente em pré-visualização. Para uma introdução aos eventos schemas, consulte [o evento Azure Event Grid schema](event-schema.md).

## <a name="available-event-types"></a>Tipos de eventos disponíveis

Uma conta Azure Key Vault gera os seguintes tipos de eventos:

| Nome completo do evento | Nome de exibição de eventos | Descrição |
| ---------- | ----------- |---|
| Microsoft.keyvault.certificatenewversionCriado | Nova versão de certificado criada | Desencadeado quando é criado um novo certificado ou nova versão de certificado. |
| Microsoft.KeyVault.CertificateNearExpiry | Certificado perto de expirar | Desencadeado quando a versão atual do certificado está prestes a expirar. (O evento é desencadeado 30 dias antes da data de validade.) |
| Microsoft.KeyVault.CertificateExpired | Certificado Expirado | Desencadeado quando o certificado expirar. |
| Microsoft.keyvault.keynewversioncriado | Nova versão criada | Desencadeado quando uma nova versão chave ou nova versão chave é criada. |
| Microsoft.KeyVault.KeyNearExpiry | Chave perto de expirar | Desencadeado quando a versão atual de uma chave está prestes a expirar. (O evento é desencadeado 30 dias antes da data de validade.) |
| Microsoft.KeyVault.KeyExpired | Chave Expirada | Ativado quando uma chave é expirada. |
| Microsoft.KeyVault.SecretNewVersionCreated | Nova versão secreta criada | Desencadeado quando uma nova versão secreta ou nova é criada. |
| Microsoft.KeyVault.SecretNearExpiry | Segredo perto de expirar | Desencadeado quando a versão atual de um segredo está prestes a expirar. (O evento é desencadeado 30 dias antes da data de validade.) |
| Microsoft.KeyVault.SecretExpired | Segredo Expirado | Desencadeado quando um segredo é expirado. |

## <a name="event-examples"></a>Exemplos de eventos

O exemplo seguinte mostra esquema para **Microsoft.KeyVault.SecretNewVersionCreated**:

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

Um evento tem os seguintes dados de alto nível:

| Propriedade | Tipo | Descrição |
| ---------- | ----------- |---|
| ID | string | A identificação do objeto que desencadeou este evento |
| nome do cofre | string | O nome chave do cofre do objeto que desencadeou este evento |
| objetoTipo | string | O tipo de objeto que desencadeou este evento |
| objectName | string | O nome do objeto que desencadeou este evento |
| versão | string | A versão do objeto que desencadeou este evento |
| nbf | número | A data não antes em segundos desde 1970-01-01T00:00:00Z do objeto que desencadeou este evento |
| exp | número | A data de validade em segundos desde 1970-01-01T00:00:00Z do objeto que desencadeou este evento |


## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grelha de Eventos?](overview.md)
* Para mais informações sobre como criar uma subscrição da Rede de Eventos Do Evento, consulte o esquema de subscrição da [Rede de Eventos](subscription-creation-schema.md).
* Para saber mais sobre a integração do Cofre chave com a Grelha de Eventos, consulte [o Cofre de Chaves de Monitorização com a Grelha de Eventos Azure (pré-visualização)](../key-vault/event-grid-overview.md).
* Para um tutorial sobre integração do Cofre chave com a Grelha de [Eventos, consulte Receber e responder a notificações chave do cofre com a Grelha de Eventos Azure (pré-visualização)](../key-vault/event-grid-tutorial.md).
* Para obter orientação adicional para key vault e automação azure, consulte:
    - [O que é o cofre de chave do Azure?](../key-vault/key-vault-overview.md)
    - [Cofre de chaves de monitorização com grelha de eventos Azure (pré-visualização)](../key-vault/event-grid-overview.md)
    - [Receber e responder a notificações chave do cofre com a Grelha de Eventos Azure (pré-visualização)](../key-vault/event-grid-tutorial.md)
    - [Visão geral da Automação Azure](../automation/index.yml)
