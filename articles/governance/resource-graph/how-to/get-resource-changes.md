---
title: Obter alterações do recurso
description: Entenda como localizar quando um recurso foi alterado e obter uma lista das propriedades que foram alteradas.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/09/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 44e7bbde40dbd4b79a6ce3735ab5a1ac81421d3b
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622565"
---
# <a name="get-resource-changes"></a>Obter alterações do recurso

Os recursos sofrem alterações ao longo da sua utilização diária, da reconfiguração ou mesmo de uma reimplementação.
A alteração pode vir de um indivíduo ou de um processo automatizado. A maioria das alterações é por design, mas, às vezes, não é. Com os últimos 14 dias do histórico de alterações, o grafo de recursos do Azure permite que você:

- Localizar quando foram detectadas alterações em uma propriedade Azure Resource Manager
- Para cada alteração de recurso, consulte detalhes de alteração de propriedade
- Veja uma comparação completa do recurso antes e depois da alteração detectada

A detecção de alterações e os detalhes são importantes para os seguintes cenários de exemplo:

- Durante o gerenciamento de incidentes para entender as alterações _potencialmente_ relacionadas. Consulte eventos de alteração durante uma janela de tempo específica e avalie os detalhes da alteração.
- Manter um banco de dados de gerenciamento de configuração, conhecido como CMDB, atualizado. Em vez de atualizar todos os recursos e seus conjuntos de propriedades completos em uma frequência agendada, só obtenha o que foi alterado.
- Entender quais outras propriedades podem ter sido alteradas quando um estado de conformidade de um recurso mudou. A avaliação dessas propriedades adicionais pode fornecer informações sobre outras propriedades que talvez precisem ser gerenciadas por meio de uma definição de Azure Policy.

Este artigo mostra como reunir essas informações por meio do SDK do grafo de recursos. Para ver essas informações na portal do Azure, consulte [histórico de alterações](../../policy/how-to/determine-non-compliance.md#change-history-preview) do Azure Policy ou [histórico de alterações](../../../azure-monitor/platform/activity-log-view.md#azure-portal)do log de atividades do Azure.
Para obter detalhes sobre as alterações em seus aplicativos da camada de infraestrutura até a implantação do aplicativo, consulte [usar a análise de alterações do aplicativo (versão prévia)](../../../azure-monitor/app/change-analysis.md) no Azure monitor.

> [!NOTE]
> Os detalhes de alteração no grafo de recursos são para as propriedades do Resource Manager. Para controlar as alterações dentro de uma máquina virtual, consulte [controle de alterações](../../../automation/automation-change-tracking.md) da automação do Azure ou [configuração de convidado do Azure Policy para VMs](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> O histórico de alterações no grafo de recursos do Azure está em visualização pública.

## <a name="find-detected-change-events-and-view-change-details"></a>Localizar eventos de alteração detectados e exibir detalhes de alteração

A primeira etapa para ver o que mudou em um recurso é localizar os eventos de alteração relacionados a esse recurso dentro de uma janela de tempo. Cada evento de alteração também inclui detalhes sobre o que mudou no recurso. Essa etapa é feita por meio do ponto de extremidade REST do **resourceChanges** .

O ponto de extremidade **resourceChanges** aceita os seguintes parâmetros no corpo da solicitação:

- **Resourceid** \[necessário\]: o recurso do Azure no qual procurar alterações.
- **interval** \[\]necessário: uma propriedade com datas de _início_ e de _término_ para quando verificar se há um evento de alteração usando o **Z (fuso horário) Zulu**.
- **fetchPropertyChanges** (opcional): uma propriedade booliana que define se o objeto de resposta inclui alterações de propriedade.

Corpo da solicitação de exemplo:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-09-28T00:00:00.000Z",
        "end": "2019-09-29T00:00:00.000Z"
    },
    "fetchPropertyChanges": true
}
```

Com o corpo da solicitação acima, o URI da API REST para **resourceChanges** é:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

A resposta é semelhante a este exemplo:

```json
{
    "changes": [
        {
            "changeId": "{\"beforeId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"beforeTime\":\"2019-09-28T00:45:35.012Z\",\"afterId\":\"6178968e-981e-4dac-ac37-340ee73eb577\",\"afterTime\":\"2019-09-28T00:52:53.371Z\"}",
            "beforeSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "afterSnapshot": {
                "snapshotId": "6178968e-981e-4dac-ac37-340ee73eb577",
                "timestamp": "2019-09-28T00:52:53.371Z"
            },
            "changeType": "Create"
        },
        {
            "changeId": "{\"beforeId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"beforeTime\":\"2019-09-28T00:43:38.366Z\",\"afterId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"afterTime\":\"2019-09-28T00:45:35.012Z\"}",
            "beforeSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "afterSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "changeType": "Delete"
        },
        {
            "changeId": "{\"beforeId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"beforeTime\":\"2019-09-28T00:43:15.518Z\",\"afterId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"afterTime\":\"2019-09-28T00:43:38.366Z\"}",
            "beforeSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "afterSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "propertyChanges": [
                {
                    "propertyName": "tags.org",
                    "afterValue": "compute",
                    "changeCategory": "User",
                    "changeType": "Insert"
                },
                {
                    "propertyName": "tags.team",
                    "afterValue": "ARG",
                    "changeCategory": "User",
                    "changeType": "Insert"
                }
            ],
            "changeType": "Update"
        },
        {
            "changeId": "{\"beforeId\":\"19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268\",\"beforeTime\":\"2019-09-28T00:42:46.839Z\",\"afterId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"afterTime\":\"2019-09-28T00:43:15.518Z\"}",
            "beforeSnapshot": {
                "snapshotId": "19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268",
                "timestamp": "2019-09-28T00:42:46.839Z"
            },
            "afterSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "propertyChanges": [{
                "propertyName": "tags.cgtest",
                "afterValue": "hello",
                "changeCategory": "User",
                "changeType": "Insert"
            }],
            "changeType": "Update"
        }
    ]
}
```

Cada evento de alteração detectado para o **ResourceId** tem as seguintes propriedades:

- **ChangeId** -esse valor é exclusivo para esse recurso. Embora a cadeia de caracteres **ChangeId** às vezes possa conter outras propriedades, só é garantido que seja exclusivo.
- **beforeSnapshot** -contém o **instantâneoid** e o **carimbo de data/hora** do instantâneo de recursos que foi realizado antes de uma alteração ser detectada.
- **afterSnapshot** -contém o **instantâneoid** e o **carimbo de data/hora** do instantâneo de recursos que foi obtido após a detecção de uma alteração.
- **ChangeType** – descreve o tipo de alteração detectada para todo o registro de alteração entre **beforeSnapshot** e **afterSnapshot**. Os valores são: _criar_, _Atualizar_e _excluir_. A matriz da propriedade **propertyChanges** só é incluída quando o **ChangeType** é _Update_.
- **propertyChanges** -essa matriz de propriedades detalha todas as propriedades de recurso que foram atualizadas entre **beforeSnapshot** e **afterSnapshot**:
  - **PropertyName** -o nome da propriedade de recurso que foi alterada.
  - **changeCategory** -descreve o que fez a alteração. Os valores são: _System_ e _User_.
  - **ChangeType** – descreve o tipo de alteração detectada para a propriedade de recurso individual.
    Os valores são: _Insert_, _Update_, _Remove_.
  - **antes** de-o valor da propriedade de recurso no **beforeSnapshot**. Não é exibido quando **altertype** é _Insert_.
  - **aftervalue** -o valor da propriedade de recurso no **afterSnapshot**. Não é exibido quando **altertype** é _Remove_.

## <a name="compare-resource-changes"></a>Comparar alterações de recursos

Com o **ChangeId** do ponto de extremidade **resourceChanges** , o ponto de extremidade REST do **resourceChangeDetails** é usado para obter os instantâneos antes e depois do recurso que foi alterado.

O ponto de extremidade **resourceChangeDetails** requer dois parâmetros no corpo da solicitação:

- **ResourceId**: o recurso do Azure para comparar as alterações.
- **ChangeId**: o evento de alteração exclusivo para o **ResourceId** coletado de **resourceChanges**.

Corpo da solicitação de exemplo:

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

Para comparar os resultados, use a propriedade **Changes** em **resourceChanges** ou avalie a parte de **conteúdo** de cada instantâneo em **resourceChangeDetails** para determinar a diferença. Se você comparar os instantâneos, o **carimbo de data/hora** sempre será mostrado como uma diferença, apesar de ser esperado.

## <a name="next-steps"></a>Passos seguintes

- Consulte o idioma em uso em [consultas de início](../samples/starter.md).
- Consulte usos avançados em [consultas avançadas](../samples/advanced.md).
- Saiba mais sobre como [explorar recursos](../concepts/explore-resources.md).