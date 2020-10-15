---
title: A estrutura dos Dashboards Azure ! Microsoft Docs
description: Caminhe pela estrutura JSON de um Painel Azure usando um painel de exemplo. Inclui referência às propriedades dos recursos.
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: mtillman
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 12/20/2019
ms.author: mblythe
ms.openlocfilehash: 014463fb0a5af639ff0da5f8db2805f9796fd928
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072480"
---
# <a name="the-structure-of-azure-dashboards"></a>A estrutura dos Dashboards do Azure
Este documento percorre a estrutura de um dashboard Azure, utilizando o seguinte painel de instrumentos como exemplo:

![dashboard de exemplo](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Uma vez que [os dashboards Azure partilhados são recursos,](../azure-resource-manager/management/overview.md)este dashboard pode ser representado como JSON.  O JSON seguinte representa o painel visualizado acima.

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

Vamos dividir as secções relevantes do JSON.  As propriedades de nível superior, __id__, __nome,__ __tipo,__ __localização__e __propriedades de tags__ são partilhadas em todos os tipos de recursos Azure. Ou seja, não têm muito a ver com o conteúdo do painel.

### <a name="the-id-property"></a>A propriedade ID

O ID de recursos Azure, sujeito às [convenções de nomeação dos recursos da Azure.](/azure/architecture/best-practices/resource-naming) Quando o portal cria um dashboard, geralmente escolhe um ID sob a forma de um guia, mas é livre de usar qualquer nome válido quando os cria programáticamente. 

### <a name="the-name-property"></a>A propriedade do nome
O nome é o segmento do ID de recurso que não inclui a informação de subscrição, tipo de recurso ou grupo de recursos. Essencialmente, é o último segmento do ID de recursos.

### <a name="the-type-property"></a>A propriedade tipo
Todos os dashboards são do tipo __Microsoft.Portal/dashboards__.

### <a name="the-location-property"></a>A propriedade de localização
Ao contrário de outros recursos, os dashboards não têm um componente de tempo de execução.  Para os dashboards, a localização indica a localização geográfica primária que armazena a representação JSON do painel de instrumentos. O valor deve ser um dos códigos de localização que podem ser recolhidos utilizando as [localizações API no recurso de subscrições](/rest/api/resources/subscriptions).

### <a name="the-tags-property"></a>A propriedade tags
As tags são uma característica comum dos recursos Azure que permitem organizar o seu recurso através de pares de valor de nome arbitrário. Para os dashboards, há uma etiqueta especial chamada __título oculto.__ Se o seu dashboard tem esta propriedade povoada, então é usado como o nome de exibição para o seu painel de instrumentos no portal. Os IDs de recursos Azure não podem ser renomeados, mas as etiquetas podem. Esta etiqueta dá-lhe uma forma de ter um nome de exibição renamível para o seu painel de instrumentos.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>O objeto de propriedades
O objeto de propriedades contém duas propriedades, __lentes__ e __metadados.__ A propriedade __das lentes__ contém informações sobre os azulejos no painel de instrumentos.  A propriedade __de metadados__ está lá para potenciais funcionalidades futuras.

### <a name="the-lenses-property"></a>A propriedade das lentes
A propriedade __das lentes__ contém o painel de instrumentos. Note que o objeto das lentes neste exemplo contém uma única propriedade chamada "0". As lentes são um conceito de agrupamento que não é atualmente implementado em dashboards. Por enquanto, todos os seus dashboards têm esta única propriedade no objeto da lente, mais uma vez, chamado "0".

### <a name="the-lens-object"></a>O objeto da lente
O objeto por baixo do "0" contém duas propriedades, __ordem__ e __partes__.  Na versão atual dos dashboards, __a ordem__ é sempre 0. A propriedade __das peças__ contém um objeto que define as partes individuais (também referidas como azulejos) no painel de instrumentos.

O objeto __de peças__ contém uma propriedade para cada parte, onde o nome da propriedade é um número. Este número não é significativo. 

### <a name="the-part-object"></a>O objeto de parte
Cada objeto de peça individual tem uma __posição,__ e __metadados.__

### <a name="the-position-object"></a>O objeto de posição
A propriedade __de posição__ contém as informações de tamanho e localização para a peça expressa em __x,__ __y,__ __rowSpan,__ e __colSpan__. Os valores são em termos de unidades de rede. Estas unidades de grelha são visíveis quando o painel está no modo de personalização, como mostrado aqui. Se quiser que um azulejo tenha uma largura de duas unidades de grelha, uma altura de uma unidade de grelha e uma localização no canto superior esquerdo do painel, então o objeto de posição é assim:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![A screenshot mostra um close-up da grelha, com uma unidade de grelha quadrada em destaque.](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>O objeto de metadados
Cada parte tem uma propriedade de metadados, um objeto tem apenas uma propriedade necessária chamada __tipo__. Esta corda diz ao portal qual azulejo mostrar. O nosso painel de exemplo utiliza este tipo de azulejos:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart` – Usado para mostrar métricas de monitorização
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart` – Costumava mostrar com texto ou imagens com formatação básica para listas, links, etc.
1. `Extension[azure]/HubsExtension/PartType/VideoPart` – Costumava mostrar vídeos do YouTube, Channel9 e qualquer outro tipo de vídeo que funciona numa videovigilância HTML.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart` – Costumava mostrar o nome e o estado de uma máquina virtual Azure.

Cada tipo de peça tem a sua própria configuração. As propriedades de configuração possíveis são chamadas __entradas,__ __configurações__e __ativo__. 

### <a name="the-inputs-object"></a>O objeto de entradas
O objeto de entradas geralmente contém informações que ligam um azulejo a uma instância de recurso.  A parte da máquina virtual no nosso painel de amostras contém uma única entrada que utiliza o ID de recurso Azure para expressar a ligação.  Este formato de ID de recursos é consistente em todos os recursos Azure.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
A parte do gráfico de métricas tem uma única entrada que expressa o recurso a que se liga, bem como informações sobre as métricas que estão a ser apresentadas. Aqui está a entrada para o azulejo que está mostrando as métricas De Rede Dentro e Rede Para Fora.

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
O objeto de definições contém os elementos configuráveis de uma peça.  No nosso painel de amostras, a parte Markdown utiliza configurações para armazenar o conteúdo de marcação personalizado, bem como um título e legenda configurável.

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

Da mesma forma, o azulejo de vídeo tem as suas próprias definições que contêm um ponteiro para o vídeo para reproduzir, uma definição de reprodução automática e informações opcionais do título.

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

### <a name="the-asset-object"></a>O objeto do ativo
Os azulejos que estão ligados a objetos de portal geríveis de primeira classe (chamados ativos) têm esta relação expressa através do objeto do ativo.  No nosso painel de exemplo, o azulejo da máquina virtual contém esta descrição do ativo.  A propriedade __idInputName__ diz ao portal que a entrada ID contém o identificador único para o ativo, neste caso o ID de recurso. A maioria dos tipos de recursos Azure têm ativos definidos no portal.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`