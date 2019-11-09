---
title: A estrutura dos painéis do Azure | Microsoft Docs
description: Este artigo explica a estrutura JSON de um painel do Azure
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: dougeby
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/01/2017
ms.author: kfollis
ms.openlocfilehash: 5933521993b598ae3758df6e2e7dbf61bf424779
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832783"
---
# <a name="the-structure-of-azure-dashboards"></a>A estrutura dos painéis do Azure
Este documento percorre a estrutura de um painel do Azure, usando o painel a seguir como exemplo:

![painel de exemplo](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Como os [painéis do Azure compartilhados são recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), esse painel pode ser representado como JSON.  O JSON a seguir representa o painel visualizado acima.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": {
            "model": {
                "timeRange": {
                    "value": {
                        "relative": {
                            "duration": 24,
                            "timeUnit": 1
                        }
                    },
                    "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
                }
            }
        }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

## <a name="common-resource-properties"></a>Propriedades de recursos comuns

Vamos dividir as seções relevantes do JSON.  As propriedades de nível superior, __ID__, __nome__, __tipo__, __local__e __marcas__ são compartilhadas entre todos os tipos de recursos do Azure. Ou seja, eles não têm muito a ver com o conteúdo do painel.

### <a name="the-id-property"></a>A propriedade ID

A ID de recurso do Azure, sujeita às [convenções de nomenclatura dos recursos do Azure](/azure/architecture/best-practices/resource-naming). Quando o portal cria um painel, ele geralmente escolhe uma ID na forma de um GUID, mas você é livre para usar qualquer nome válido ao criá-los programaticamente. 

### <a name="the-name-property"></a>A propriedade Name
O nome é o segmento da ID do recurso que não inclui a assinatura, o tipo de recurso ou as informações do grupo de recursos. Basicamente, é o último segmento da ID do recurso.

### <a name="the-type-property"></a>A propriedade Type
Todos os painéis são do tipo __Microsoft. Portal/dashboards__.

### <a name="the-location-property"></a>A propriedade Location
Ao contrário de outros recursos, os painéis não têm um componente de tempo de execução.  Para painéis, o local indica a localização geográfica primária que armazena a representação JSON do painel. O valor deve ser um dos códigos de localização que podem ser buscados usando a [API Locations no recurso de assinaturas](https://docs.microsoft.com/rest/api/resources/subscriptions).

### <a name="the-tags-property"></a>A propriedade Tags
As marcas são um recurso comum dos recursos do Azure que permitem organizar seu recurso por pares de valor de nome arbitrário. Para painéis, há uma marca especial denominada __Hidden-título__. Se o seu painel tiver essa propriedade populada, ela será usada como o nome de exibição do seu painel no Portal. As IDs de recursos do Azure não podem ser renomeadas, mas as marcas podem. Essa marca fornece uma maneira de ter um nome de exibição de renomeável para seu painel.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>O objeto Properties
O objeto Properties contém duas propriedades, __lentes__ e __metadados__. A propriedade __lentes__ contém informações sobre os blocos (também conhecido como partes) no painel.  A propriedade __Metadata__ está lá para possíveis recursos futuros.

### <a name="the-lenses-property"></a>A propriedade lentes
A propriedade __lentes__ contém o painel. Observe que o objeto de lentes neste exemplo contém uma única propriedade chamada "0". As lentes são um conceito de agrupamento que não está implementado atualmente em painéis. Por enquanto, todos os seus painéis têm essa propriedade única no objeto Lens, novamente, chamado "0".

### <a name="the-lens-object"></a>O objeto de lente
O objeto sob o "0" contém duas propriedades, __Order__ e __Parts__.  Na versão atual dos painéis, o __pedido__ é sempre 0. A propriedade __Parts__ contém um objeto que define as partes individuais (também conhecido como blocos) no painel.

O objeto __Parts__ contém uma propriedade para cada parte, em que o nome da propriedade é um número. Esse número não é significativo. 

### <a name="the-part-object"></a>O objeto da parte
Cada objeto da parte individual tem uma __posição__e __metadados__.

### <a name="the-position-object"></a>O objeto Position
A propriedade __Position__ contém as informações de tamanho e local da parte expressa como __x__, __y__, __RowSpan__e __ColSpan__. Os valores estão em termos de unidades de grade. Essas unidades de grade ficam visíveis quando o painel está no modo de personalização, como mostrado aqui. Se você quiser que um bloco tenha uma largura de duas unidades de grade, uma altura de uma unidade de grade e um local no canto superior esquerdo do painel, o objeto de posição terá esta aparência:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![unidades de grade](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>O objeto de metadados
Cada parte tem uma propriedade de metadados, um objeto tem apenas uma propriedade necessária chamada __Type__. Essa cadeia de caracteres informa ao Portal qual bloco mostrar. Nosso painel de exemplo usa estes tipos de blocos:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart` – usado para mostrar as métricas de monitoramento
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart` – usado para mostrar com texto ou imagens com formatação básica para listas, links, etc.
1. `Extension[azure]/HubsExtension/PartType/VideoPart` – usado para mostrar vídeos do YouTube, channel9 e qualquer outro tipo de vídeo que funcione em uma marca de vídeo HTML.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart` – usado para mostrar o nome e o status de uma máquina virtual do Azure.

Cada tipo de parte tem sua própria configuração. As propriedades de configuração possíveis são chamadas de __entradas__, __configurações__e __ativos__. 

### <a name="the-inputs-object"></a>O objeto de entradas
O objeto de entradas geralmente contém informações que associam um bloco a uma instância de recurso.  A parte da máquina virtual em nosso painel de exemplo contém uma única entrada que usa a ID de recurso do Azure para expressar a associação.  Esse formato de ID de recurso é consistente em todos os recursos do Azure.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
A parte do gráfico de métricas tem uma única entrada que expressa o recurso para associar, bem como informações sobre as métricas que estão sendo exibidas. Aqui está a entrada para o bloco que mostra as métricas de rede e saída de rede.

```json
“inputs”:
[
    {
        "name": "queryInputs",
        "value": 
        {
            "timespan": 
            {
                "duration": "PT1H",
                "start": null,
                "end": null
           },
            "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
            "chartType": 0,
            "metrics": 
            [
                {
                    "name": "Network In",
                    "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                },
                {
                    "name": "Network Out",
                    "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                }
            ]
        }
    }
]

```

### <a name="the-settings-object"></a>O objeto de configurações
O objeto Settings contém os elementos configuráveis de uma parte.  Em nosso painel de exemplo, a parte de redução usa as configurações para armazenar o conteúdo de redução personalizada, bem como um título e subtítulo configuráveis.

```json
"settings": 
{
    "content": 
    {
        "settings": 
        {
            "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
            "title": "Test VM Dashboard",
            "subtitle": "Contoso"
        }
    }
}

```

Da mesma forma, o bloco de vídeo tem suas próprias configurações que contêm um ponteiro para o vídeo a ser reproduzido, uma configuração de reprodução automática e informações de título opcionais.

```json
"settings": 
{
   "content": 
    {
        "settings": 
        {
            "title": "",
            "subtitle": "",
            "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
            "autoplay": false
        }
    }
}

```

### <a name="the-asset-object"></a>O objeto Asset
Blocos que são associados a objetos de portal gerenciáveis de primeira classe (chamados ativos) têm essa relação expressa por meio do objeto de ativo.  Em nosso painel de exemplo, o bloco da máquina virtual contém essa descrição do ativo.  A propriedade __idInputName__ informa ao portal que a entrada de ID contém o identificador exclusivo para o ativo, nesse caso, a ID do recurso. A maioria dos tipos de recursos do Azure tem ativos definidos no Portal.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`
