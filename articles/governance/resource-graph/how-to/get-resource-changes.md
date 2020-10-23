---
title: Obter alterações do recurso
description: Entenda como encontrar quando um recurso foi alterado, obter uma lista das propriedades que mudaram, e avaliar os diffs.
ms.date: 10/14/2020
ms.topic: how-to
ms.openlocfilehash: 70213caeaf71e1adc5a11ec0e9cbadfea032dca4
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92203471"
---
# <a name="get-resource-changes"></a>Obter alterações do recurso

Os recursos são alterados através do curso de uso diário, reconfiguração e até mesmo redistribuição.
A mudança pode vir de um indivíduo ou de um processo automatizado. A maioria das mudanças é por desígnio, mas às vezes não é. Com os últimos 14 dias de história da mudança, o Azure Resource Graph permite-lhe:

- Saiba quando foram detetadas alterações numa propriedade do Azure Resource Manager
- Para cada alteração de recursos, consulte os detalhes da alteração de propriedade
- Consulte uma comparação completa do recurso antes e depois da alteração detetada

A deteção de alterações e os detalhes são valiosos para os seguintes cenários de exemplo:

- Durante a gestão de incidentes para entender alterações _potencialmente_ relacionadas. Consulta para alterar eventos durante uma janela de tempo específica e avaliar os detalhes da mudança.
- Manter uma Base de Dados de Gestão de Configuração, conhecida como CMDB, atualizada. Em vez de refrescar todos os recursos e seus conjuntos de propriedades completas em uma frequência programada, apenas obter o que mudou.
- Compreender que outras propriedades podem ter sido alteradas quando um estado de conformidade alterado de recurso. A avaliação destas propriedades adicionais pode fornecer insights sobre outras propriedades que podem precisar de ser geridas através de uma definição de Política Azure.

Este artigo mostra como recolher esta informação através do SDK do Resource Graph. Para ver estas informações no portal Azure, consulte o histórico de Mudança de [Atividade](../../policy/how-to/determine-non-compliance.md#change-history) da Azure Policy ou o histórico de [alteração](../../../azure-monitor/platform/activity-log.md#view-the-activity-log)de registo de atividades Azure . Para obter detalhes sobre as alterações às suas aplicações a partir da camada de infraestrutura até à implementação da aplicação, consulte [Use Application Change Analysis (preview)](../../../azure-monitor/app/change-analysis.md) no Azure Monitor.

> [!NOTE]
> Os detalhes da alteração no Gráfico de Recursos são para propriedades do Gestor de Recursos. Para rastrear alterações dentro de uma máquina virtual, consulte o rastreio de [mudança](../../../automation/change-tracking/overview.md) da Azure Automation ou a configuração de hóspedes da Azure Policy [para VMs](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> Alterar o histórico no Gráfico de Recursos Azure está em Visualização Pública.

## <a name="find-detected-change-events-and-view-change-details"></a>Encontre eventos de alteração detetados e veja detalhes da mudança

O primeiro passo para ver o que mudou num recurso é encontrar os eventos de mudança relacionados com esse recurso dentro de uma janela de tempo. Cada evento de alteração também inclui detalhes sobre o que mudou no recurso. Este passo é feito através do ponto final **de rest de recursos.**

O ponto final **resourceChanges** aceita os seguintes parâmetros no organismo de pedido:

- **recursosId** \[ requer : \] O recurso Azure para procurar alterações.
- **intervalo** \[ requerido \] : Uma propriedade com datas de _início_ e _fim_ para quando verificar um evento de mudança usando o **Fuso Horário Zulu (Z)**.
- **fetchPropertyChanges** (opcional): Uma propriedade booleana que define se o objeto de resposta inclui alterações de propriedade.

Corpo de pedido de exemplo:

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

Com o organismo de pedido acima, o REST API URI para **recursosChanges** é:

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

- **changeId** - Este valor é exclusivo desse recurso. Embora a cadeia **changeId** possa por vezes conter outras propriedades, é apenas garantido que é único.
- **beforeSnapshot** - Contém o **snapshotId** e **o timetamp** do instantâneo de recurso que foi tirado antes de uma mudança ser detetada.
- **afterSnapshot** - Contém o **snapshotId** e o **timetamp** do instantâneo de recurso que foi tirado após a deteção de uma alteração.
- **changeType** - Descreve o tipo de alteração detetada para todo o registo de alteração entre o **antes de Snapshot** e **afterSnapshot**. Os valores são: _Criar,_ _atualizar_e _eliminar_. O conjunto de **propriedadesChanges** só está incluído quando **o ChangeType** é _Update_.
- **propertyChanges** - Esta variedade de propriedades detalha todas as propriedades de recursos que foram atualizadas entre o **antes de Snapshot** e o **afterSnapshot**:
  - **nome da propriedade** - O nome da propriedade de recurso que foi alterado.
  - **changeCategoria** - Descreve o que fez a mudança. Os valores são: _Sistema_ e _Utilizador._
  - **changeType** - Descreve o tipo de alteração detetada para a propriedade de recursos individuais.
    Os valores são: _Inserir,_ _Atualizar,_ _Remover_.
  - **antes do Valor -** O valor da propriedade de recurso no **anterior**. Não é apresentado quando **o alterarType** é _Inserir_.
  - **afterValue** - O valor da propriedade de recurso no **afterSnapshot**. Não é apresentado quando **o 'Alterar'** é _Remover_.

## <a name="compare-resource-changes"></a>Comparar alterações de recursos

Com o **changeId** do ponto final **de recursosChanges,** o ponto final rest do **recursoChangeDetails** é então utilizado para obter os instantâneos antes e depois do recurso que foi alterado.

O ponto final **do recursoChangeDetails** requer dois parâmetros no organismo de pedido:

- **resourceId**: O recurso Azure para comparar alterações.
- **changeId**: O evento de alteração única para os **recursosId** recolhidos a partir de **recursosChanges**.

Corpo de pedido de exemplo:

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

**antes de Snapshot** e **afterSnapshot** cada dar o tempo que o instantâneo foi tirado e as propriedades naquele momento. A mudança aconteceu em algum momento entre estas fotos. Olhando para o exemplo anterior, podemos ver que a propriedade que mudou foi **suportahttpsTrafficOnly.**

Para comparar os resultados, utilize a propriedade **alterações** em **recursosChanges** ou avalie a parte de **conteúdo** de cada instantâneo em **recursosChangeDetails** para determinar a diferença. Se compararmos os instantâneos, a **hora da hora** mostra sempre uma diferença, apesar de ser esperada.

## <a name="next-steps"></a>Passos seguintes

- Consulte o idioma em uso nas [consultas de arranque](../samples/starter.md).
- Consulte utilizações avançadas em [consultas avançadas.](../samples/advanced.md)
- Saiba mais sobre como [explorar recursos.](../concepts/explore-resources.md)
- Para obter orientações sobre o trabalho com consultas em alta frequência, consulte [orientação para pedidos acelerados](../concepts/guidance-for-throttled-requests.md).
