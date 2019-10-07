---
title: Obter alterações do recurso
description: Entenda como localizar quando um recurso foi alterado e obter uma lista das propriedades que foram alteradas.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 2027f56d44be14895a40550d78a79d9e9dda9d97
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980302"
---
# <a name="get-resource-changes"></a>Obter alterações do recurso

Os recursos são alterados com o curso de uso diário, reconfiguração e até mesmo a reimplantação.
A alteração pode vir de um indivíduo ou de um processo automatizado. A maioria das alterações é por design, mas, às vezes, não é. Com os últimos 14 dias do histórico de alterações, o grafo de recursos do Azure permite que você:

- Descubra quando as alterações foram detetadas numa propriedade do Azure Resource Manager.
- Veja as propriedades que mudaram como parte desse evento de alteração.

A detecção de alterações e os detalhes são importantes para os seguintes cenários de exemplo:

- Durante o gerenciamento de incidentes para entender as alterações _potencialmente_ relacionadas. Consulte eventos de alteração durante uma janela de tempo específica e avalie os detalhes da alteração.
- Manter um banco de dados de gerenciamento de configuração, conhecido como CMDB, atualizado. Em vez de atualizar todos os recursos e seus conjuntos de propriedades completos em uma frequência agendada, só obtenha o que foi alterado.
- Entender quais outras propriedades podem ter sido alteradas quando um estado de conformidade de um recurso mudou. A avaliação dessas propriedades adicionais pode fornecer informações sobre outras propriedades que talvez precisem ser gerenciadas por meio de uma definição de Azure Policy.

Este artigo mostra como reunir essas informações por meio do SDK do grafo de recursos. Para ver essas informações na portal do Azure, consulte [histórico de alterações](../../policy/how-to/determine-non-compliance.md#change-history-preview) do Azure Policy ou [histórico de alterações](../../../azure-monitor/platform/activity-log-view.md#azure-portal)do log de atividades do Azure.

> [!NOTE]
> Os detalhes de alteração no grafo de recursos são para as propriedades do Resource Manager. Para controlar as alterações dentro de uma máquina virtual, consulte [controle de alterações](../../../automation/automation-change-tracking.md) da automação do Azure ou [configuração de convidado do Azure Policy para VMs](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> O histórico de alterações no grafo de recursos do Azure está em visualização pública.

## <a name="find-when-changes-were-detected"></a>Localizar quando foram detectadas alterações

A primeira etapa para ver o que mudou em um recurso é localizar os eventos de alteração relacionados a esse recurso dentro de uma janela de tempo. Essa etapa é feita por meio do ponto de extremidade REST do **resourceChanges** .

O ponto de extremidade **resourceChanges** requer dois parâmetros no corpo da solicitação:

- **resourceId**: O recurso do Azure no qual procurar alterações.
- **interval**: Uma propriedade com datas de _início_ e de _término_ para quando verificar um evento de alteração usando o **fuso horário Zulu (Z)** .

Corpo do pedido de exemplo:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-03-28T00:00:00.000Z",
        "end": "2019-03-31T00:00:00.000Z"
    }
}
```

Com o corpo da solicitação acima, o URI da API REST para **resourceChanges** é:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

A resposta é semelhante a este exemplo:

```json
{
    "changes": [{
            "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
            "beforeSnapshot": {
                "timestamp": "2019-03-29T01:32:05.993Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-29T01:54:24.42Z"
            }
        },
        {
            "changeId": "9dc352cb-b7c1-4198-9eda-e5e3ed66aec8",
            "beforeSnapshot": {
                "timestamp": "2019-03-28T10:30:19.68Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-28T21:12:31.337Z"
            }
        }
    ]
}
```

Cada evento de alteração detectado para o **ResourceId** tem uma **ChangeId** que é exclusiva para esse recurso. Embora a cadeia de caracteres **ChangeId** às vezes possa conter outras propriedades, só é garantido que seja exclusivo. O registro de alteração inclui as horas em que os instantâneos antes e depois foram tirados.
O evento de alteração ocorreu em algum momento nesta janela de tempo.

## <a name="see-what-properties-changed"></a>Ver quais propriedades foram alteradas

Com o **ChangeId** do ponto de extremidade **resourceChanges** , o ponto de extremidade REST do **resourceChangeDetails** é usado para obter informações específicas do evento de alteração.

O ponto de extremidade **resourceChangeDetails** requer dois parâmetros no corpo da solicitação:

- **resourceId**: O recurso do Azure no qual procurar alterações.
- **changeId**: O evento de alteração exclusivo para o **ResourceId** coletado de **resourceChanges**.

Corpo do pedido de exemplo:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

Com o corpo da solicitação acima, o URI da API REST para **resourceChangeDetails** é:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

A resposta é semelhante a este exemplo:

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": false,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

**beforeSnapshot** e **afterSnapshot** cada um fornece o tempo que o instantâneo foi tirado e as propriedades nesse momento. A alteração ocorreu em algum ponto entre esses instantâneos. Observando o exemplo acima, podemos ver que a propriedade alterada foi **supportsHttpsTrafficOnly**.

Para comparar os resultados programaticamente, compare a parte de **conteúdo** de cada instantâneo para determinar a diferença. Se você comparar todo o instantâneo, o **carimbo de data/hora** sempre será mostrado como uma diferença, apesar de ser esperado.

## <a name="next-steps"></a>Passos seguintes

- Consulte o idioma em uso em [consultas de início](../samples/starter.md).
- Consulte usos avançados em [consultas avançadas](../samples/advanced.md).
- Aprenda a [explorar os recursos](../concepts/explore-resources.md).