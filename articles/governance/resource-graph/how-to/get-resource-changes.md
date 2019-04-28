---
title: Obter alterações do recurso
description: Compreender como encontrar quando um recurso foi alterado e obter uma lista das propriedades que foram alterados.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/20/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 0ae85b45dfcd80056316ed5f2099aab4057d24c8
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63760807"
---
# <a name="get-resource-changes"></a>Obter alterações do recurso

Recursos sejam alterados durante o curso de utilização diária, reconfiguração e até mesmo a reimplementação.
Alteração pode vir de uma pessoa ou por um processo automatizado. A maioria das alterações é por design, mas, às vezes, não é. Com os últimos 14 dias do histórico de alterações, o gráfico de recursos do Azure permite-lhe:

- Descubra quando as alterações foram detetadas numa propriedade do Azure Resource Manager.
- Veja as propriedades que mudaram como parte desse evento de alteração.

Deteção de alteração e os detalhes são valiosos para os seguintes cenários de exemplo:

- Durante a gestão de incidentes para compreender _potencialmente_ outras alterações relacionadas. Consultar os eventos de alteração durante um período específico de tempo e avaliar os detalhes de alteração.
- Manter um banco de dados de gestão de configuração, conhecido como CMDB, atualizado. Em vez de atualizar todos os recursos e seus conjuntos de propriedades completa numa frequência agendada, obtenha apenas o que foi alterado.
- Noções básicas sobre as outras propriedades que possam ter sido alteradas quando um recurso alterou o estado de conformidade. Avaliação destas propriedades adicionais pode fornecer informações sobre outras propriedades que poderão ter de ser geridos através de uma definição de política do Azure.

Este artigo mostra como recolher estas informações através do SDK do gráfico de recursos. Para ver estas informações no portal do Azure, veja o Azure Policy [histórico de alterações](../../policy/how-to/determine-non-compliance.md#change-history-preview).

> [!NOTE]
> Detalhes da alteração no gráfico de recursos são as propriedades do Gestor de recursos. Para controlar as alterações numa máquina virtual, consulte da automatização do Azure [controlo de alterações](../../../automation/automation-change-tracking.md) ou o Azure Policy [configuração de convidado para VMs](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> Histórico de alterações no gráfico de recursos do Azure está em pré-visualização pública.

## <a name="find-when-changes-were-detected"></a>Encontrar quando foram detetadas alterações

O primeiro passo para ver o que foi alterado num recurso é encontrar os eventos de alteração relacionados a esse recurso numa janela de tempo. Este passo é feito através da **resourceChanges** ponto final REST.

O **resourceChanges** ponto final requer dois parâmetros no corpo do pedido:

- **resourceId**: O recurso do Azure para verificar as alterações.
- **interval**: Uma propriedade com o _começar_ e _final_ datas para quando verificar a existência de um evento de alteração utilizando o **Zulu fuso horário (Z)**.

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

Com o corpo do pedido acima, o URI da API REST para **resourceChanges** é:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

A resposta é semelhante a este exemplo:

```json
{
    "changes": [{
            "changeId": "2db0ad2d-f6f0-4f46-b529-5c4e8c494648",
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

Cada detetou que o evento de alteração para o **resourceId** tem uma **changeId** que seja exclusivo a esse recurso. Embora o **changeId** cadeia de caracteres, às vezes, pode conter outras propriedades, só há garantia de ser exclusivo. O registo de alteração inclui as vezes que o antes e depois que foram tirados instantâneos.
Ocorreu o evento de alteração em algum ponto nessa janela de tempo.

## <a name="see-what-properties-changed"></a>Veja o que as propriedades alteradas

Com o **changeId** partir a **resourceChanges** ponto de extremidade, o **resourceChangeDetails** ponto final REST, em seguida, é utilizado para obter informações específicas do evento de alteração.

O **resourceChangeDetails** ponto final requer dois parâmetros no corpo do pedido:

- **resourceId**: O recurso do Azure para verificar as alterações.
- **changeId**: O evento de alteração exclusivo para o **resourceId** reunidas a partir de **resourceChanges**.

Corpo do pedido de exemplo:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "53dc0515-b86b-4bc2-979b-e4694ab4a556"
}
```

Com o corpo do pedido acima, o URI da API REST para **resourceChangeDetails** é:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

A resposta é semelhante a este exemplo:

```json
{
    "changeId": "53dc0515-b86b-4bc2-979b-e4694ab4a556",
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

**beforeSnapshot** e **afterSnapshot** cada dar o tempo que o instantâneo foi criado e as propriedades nesse momento. A alteração ocorreu em algum ponto entre estes instantâneos. Examinando o exemplo acima, podemos ver que a propriedade que foi alterada foi **supportsHttpsTrafficOnly**.

Para comparar os resultados por meio de programação, compare a **conteúdo** parte de cada instantâneo para determinar a diferença. Se comparar o instantâneo inteiro, o **timestamp** sempre mostra como uma diferença, apesar de que está a ser esperado.

## <a name="next-steps"></a>Passos Seguintes

- Consulte o idioma utilizado na [consultas Starter](../samples/starter.md).
- Consulte avançada usa [avançadas consultas](../samples/advanced.md).
- Aprenda a [explore os recursos](../concepts/explore-resources.md).
