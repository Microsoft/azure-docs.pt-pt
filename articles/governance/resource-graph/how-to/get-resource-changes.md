---
title: Obter alterações do recurso
description: Entenda como descobrir quando um recurso foi alterado, obtenha uma lista das propriedades que mudaram e avalie as difusões.
ms.date: 10/09/2019
ms.topic: how-to
ms.openlocfilehash: 9504ac77fc4a3b03434912cc65284e2001df6e03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873034"
---
# <a name="get-resource-changes"></a>Obter alterações do recurso

Os recursos são alterados através do curso do uso diário, reconfiguração e até mesmo redistribuição.
A mudança pode vir de um indivíduo ou de um processo automatizado. A maioria das mudanças é por design, mas às vezes não é. Com os últimos 14 dias de história da mudança, o Azure Resource Graph permite-lhe:

- Descubra quando foram detetadas alterações numa propriedade do Gestor de Recursos Azure
- Para cada mudança de recursos, consulte detalhes de mudança de propriedade
- Ver uma comparação completa do recurso antes e depois da mudança detetada

A deteção de alterações e os detalhes são valiosos para os seguintes cenários de exemplo:

- Durante a gestão de incidentes para compreender alterações _potencialmente_ relacionadas. Consulta para eventos de mudança durante uma janela de tempo específica e avaliar os detalhes da mudança.
- Manter uma base de dados de gestão de configuração, conhecida como CMDB, atualizada. Em vez de refrescar todos os recursos e os seus conjuntos de propriedades completas numa frequência programada, só obter o que mudou.
- Compreender que outras propriedades podem ter sido alteradas quando um recurso mudou o estado de conformidade. A avaliação destas propriedades adicionais pode fornecer informações sobre outras propriedades que possam precisar de ser geridas através de uma definição de Política Azure.

Este artigo mostra como recolher esta informação através do SDK do Resource Graph. Para ver esta informação no portal Azure, consulte o histórico de mudança de [atividade](../../policy/how-to/determine-non-compliance.md#change-history-preview) da Azure Policy ou o histórico de [alterações](../../../azure-monitor/platform/activity-log-view.md#azure-portal)de registo de atividade sinuosa.
Para mais detalhes sobre as alterações nas suas aplicações desde a camada de infraestrutura até à implementação da aplicação, consulte a Análise de Alterações de [Aplicações (pré-visualização)](../../../azure-monitor/app/change-analysis.md) no Monitor Azure.

> [!NOTE]
> Alterar detalhes no Graph de Recursos são para propriedades do Gestor de Recursos. Para rastrear alterações dentro de uma máquina virtual, consulte o rastreio de [mudança](../../../automation/automation-change-tracking.md) da Azure Automation ou a configuração de hóspedes da Azure Policy [para VMs](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> A história da mudança no Gráfico de Recursos Azure está em Pré-visualização Pública.

## <a name="find-detected-change-events-and-view-change-details"></a>Encontre eventos de mudança detetados e veja detalhes de mudança

O primeiro passo para ver o que mudou num recurso é encontrar os eventos de mudança relacionados com esse recurso dentro de uma janela de tempo. Cada evento de mudança também inclui detalhes sobre o que mudou no recurso. Este passo é feito através do ponto final do REST **Changes.**

O ponto final **alterações** de recursos aceita os seguintes parâmetros no organismo de pedido:

- **recursosId** \[necessário\]: O recurso Azure para procurar alterações.
- **intervalo** \[necessário\]: Uma propriedade com datas de _início_ e _fim_ para quando verificar se há um evento de mudança utilizando o **Fuso horário zulu (Z)**.
- **fetchPropertyChanges** (opcional): Uma propriedade booleana que se define se o objeto de resposta inclui alterações de propriedade.

Órgão de pedido de exemplo:

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

Com o organismo de pedido acima, o REST API URI para **recursosAlterações** é:

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

Cada evento de alteração detetado para o **recursoId** tem as seguintes propriedades:

- **changeId** - Este valor é único a esse recurso. Embora a cadeia **changeId** possa, por vezes, conter outras propriedades, é garantido que é única.
- **beforeSnapshot** - Contém o **instantâneo** e **o carimbo** de tempo do instantâneo de recursos que foi tirado antes de ser detetada uma alteração.
- **afterSnapshot** - Contém o **instantâneo e** **o carimbo** temporal do instantâneo de recursos que foi tirado após uma alteração ter sido detetada.
- **changeType** - Descreve o tipo de alteração detetado para todo o registo de alteração entre o **antes do Snapshot** e o **afterSnapshot**. Os valores são: _Criar,_ _Atualizar_e _Apagar_. A **matriz de propriedadesMuda** apenas está incluída quando **o changeType** é _Update_.
- **propriedadesChanges** - Este conjunto de propriedades detalha todas as propriedades de recursos que foram atualizadas entre o **antes do Snapshot** e o **afterSnapshot**:
  - **propertyName** - O nome da propriedade de recursos que foi alterado.
  - **changeCategory** - Descreve o que fez a mudança. Os valores são: _Sistema_ e _Utilizador._
  - **changeType** - Descreve o tipo de alteração detetado para a propriedade de recursos individuais.
    Os valores são: _Inserir,_ _Atualizar,_ _Remover_.
  - **beforeValue** - O valor da propriedade de recursos no **antes snapshot**. Não é apresentado quando **o changeType** é _Inserir_.
  - **afterValue** - O valor da propriedade de recursos no **afterSnapshot**. Não é apresentado quando **o changeType** é _Remover_.

## <a name="compare-resource-changes"></a>Comparar alterações de recursos

Com o **changeId** do ponto final do **recursoChanges,** o ponto final do Rest **ChangeDetails** de recurso é então usado para obter as imagens antes e posteriores do recurso que foi alterado.

O ponto final **do recursoChangeDetails** requer dois parâmetros no organismo de pedido:

- **recursosId**: O recurso Azure para comparar alterações.
- **changeId**: O evento de mudança único para o **recursoId** recolhido a partir de **recursosChanges**.

Órgão de pedido de exemplo:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"afterTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

Com o organismo de pedido acima, o REST API URI para **recursosChangeDetails** é:

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

**beforeSnapshot** e **afterSnapshot** cada um dá o tempo que o instantâneo foi tirado e as propriedades da época. A mudança aconteceu em algum momento entre estes instantâneos. Olhando para o exemplo acima, podemos ver que a propriedade que mudou foi **suportaHttpsTrafficOnly**.

Para comparar os resultados, utilize a propriedade das **alterações** em **recursosAltera** ou avalie a parte de **conteúdo** de cada instantâneo em **recursosChangeDetails** para determinar a diferença. Se comparar mossas, a marca de **tempo** mostra sempre como uma diferença, apesar de ser esperado.

## <a name="next-steps"></a>Passos seguintes

- Consulte o idioma utilizado nas [consultas de arranque](../samples/starter.md).
- Consulte [utilizações avançadas em consultas avançadas.](../samples/advanced.md)
- Saiba mais sobre como [explorar recursos.](../concepts/explore-resources.md)