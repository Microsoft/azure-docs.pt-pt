---
title: A estrutura dos Dashboards do Azure | Documentos da Microsoft
description: Este artigo explica a estrutura JSON de um Dashboard do Azure
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
ms.openlocfilehash: a7e9acbe78ffdca2e615873cc4c33f86b250a429
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60551519"
---
# <a name="the-structure-of-azure-dashboards"></a>A estrutura dos Dashboards do Azure
Este documento aborda a estrutura de um dashboard do Azure, utilizando o dashboard seguinte como exemplo:

![dashboard de exemplo](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Uma vez que partilhou [dashboards do Azure são recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), este dashboard pode ser representado como JSON.  O JSON seguinte representa o dashboard visualizado acima.

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

Vamos dividir as secções relevantes do JSON.  As propriedades de nível superior, __id__, __nome__, __tipo__, __localização__, e __etiquetas__ propriedades são partilhado em todos os tipos de recursos do Azure. Ou seja, não têm muito o que fazer com o conteúdo do dashboard.

### <a name="the-id-property"></a>A propriedade de id

O id de recurso do Azure, sujeita-se para o [convenções de nomenclatura do recursos do Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Quando o portal cria um dashboard que ele geralmente escolhe um id na forma de um guid, mas tem liberdade para utilizar qualquer nome válido quando criá-los por meio de programação. 

### <a name="the-name-property"></a>A propriedade de nome
O nome é o segmento do recurso Id que não inclui a subscrição, tipo de recurso ou informações de grupo de recursos. Essencialmente, é o último segmento do id do recurso.

### <a name="the-type-property"></a>A propriedade de tipo
Todos os dashboards são do tipo __Microsoft.Portal/dashboards__.

### <a name="the-location-property"></a>A propriedade de localização
Ao contrário de outros recursos, os dashboards não tem um componente de tempo de execução.  Para dashboards, a localização indica a localização geográfica principal que armazena a representação de JSON do dashboard. O valor deve ser um dos códigos de localização que podem ser obtidos com o [localizações API no recurso subscrições](https://docs.microsoft.com/rest/api/resources/subscriptions).

### <a name="the-tags-property"></a>A propriedade de etiquetas
As etiquetas são uma funcionalidade comum de recursos do Azure que lhe permite organizar seu recurso por pares de valor de nome arbitrário. Para dashboards, existe uma etiqueta especial chamada __title oculto__. Se o dashboard tiver esta propriedade preenchida, em seguida, é utilizado como o nome a apresentar para o seu dashboard no portal. Não não possível mudar o nome de Ids de recurso do Azure, mas podem de etiquetas. Esta etiqueta dá-lhe uma forma de ter um nome a apresentar renamable para o seu dashboard.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>O objecto de propriedades
O objecto de propriedades contém duas propriedades, __lentes__ e __metadados__. O __lentes__ propriedade contém informações sobre os mosaicos (também conhecido como partes) no dashboard.  O __metadados__ propriedade existe para potenciais futuras funcionalidades.

### <a name="the-lenses-property"></a>A propriedade de lentes
O __lentes__ propriedade contém o dashboard. Tenha em atenção que os pontos de vista certos objeto neste exemplo contém uma única propriedade chamada "0". Lentes são um conceito de agrupamento que não está atualmente implementado nos dashboards. Por agora, todos os seus dashboards têm esta única propriedade no objeto de lente, novamente, chamada "0".

### <a name="the-lens-object"></a>O objeto de lente
O objeto por baixo do "0" contém duas propriedades, __ordem__ e __partes__.  Na versão atual de dashboards, __ordem__ é sempre 0. O __partes__ propriedade contém um objeto que define as partes individuais (também conhecido como mosaicos) no dashboard.

O __partes__ objeto contém uma propriedade para cada parte, em que o nome da propriedade é um número. Este número não é significativo. 

### <a name="the-part-object"></a>O objeto de parte
Cada objeto de parte individual tem um __posição__, e __metadados__.

### <a name="the-position-object"></a>O objeto de posição
O __posição__ propriedade contém as informações de tamanho e localização para a parte expressa __x__, __y__, __rowSpan__e o __colSpan__. Os valores são em termos de unidades de grade. Estas unidades de grelha estão visíveis quando o dashboard está no modo de personalizar, conforme mostrado aqui. Se pretender que um mosaico para ter uma largura de duas unidades de grade, uma altura de unidade de uma grade e uma localização no canto superior esquerdo do dashboard, em seguida, o objeto de posição tem o seguinte aspeto:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![unidades de grelha](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>O objeto de metadados
Cada parte tem uma propriedade de metadados, um objeto tem apenas uma propriedade necessária chamada __tipo__. Esta cadeia informa ao portal do mosaico para mostrar. Nosso dashboard de exemplo usa esses tipos de mosaicos:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart` – Utilizada para mostrar métricas de monitorização
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart` – Utilizada para mostrar com texto ou imagens com formatação básicos para listas, ligações, etc.
1. `Extension[azure]/HubsExtension/PartType/VideoPart` – Utilizado para mostrar o vídeos do YouTube, do Channel 9 e qualquer outro tipo de vídeo que funciona numa etiqueta de vídeo de HTML.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart` – Utilizado para mostrar o nome e o estado de uma máquina virtual do Azure.

Cada tipo de parte tem sua própria configuração. As propriedades de configuração possíveis são chamadas __entradas__, __definições__, e __asset__. 

### <a name="the-inputs-object"></a>O objeto de entradas
O objeto de entradas geralmente contém informações que vincula um mosaico para uma instância de recurso.  A parte de máquina virtual no nosso dashboard de exemplo contém uma entrada única que utiliza o id de recurso do Azure para expressar o enlace.  Este formato de id de recurso é consistente em todos os recursos do Azure.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
A parte do gráfico de métricas tem uma entrada única que expresse o recurso para vincular a, assim como informações sobre o metric(s) sendo exibida. Segue-se a entrada para o mosaico que mostra as métricas de rede na rede e saída.

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

### <a name="the-settings-object"></a>O objeto de definições
O objeto settings contém os elementos configuráveis de uma parte.  No nosso dashboard de exemplo, a parte de Markdown utiliza definições para armazenar a conteúdo, bem como um configurável título e subtítulo personalizada de markdown.

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

Da mesma forma, o mosaico de vídeo tem suas próprias definições que contêm um ponteiro para o vídeo seja reproduzido, uma definição de reprodução automática e informações de título opcional.

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

### <a name="the-asset-object"></a>O objeto de ativo
Mosaicos ligados a primeira categoria gerenciáveis objetos portais (chamados de ativos) tem esta relação expressa via o objeto de ativo.  No nosso dashboard de exemplo, o mosaico de máquina virtual contém essa descrição do recurso.  O __idInputName__ propriedade informa ao portal do que o id de entrada contém o identificador exclusivo para o elemento, nesse caso, o id de recurso. Tipos de recursos Azure mais tem recursos definidos no portal.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`
