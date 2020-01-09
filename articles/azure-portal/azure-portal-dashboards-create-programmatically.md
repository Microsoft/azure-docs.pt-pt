---
title: Criar programaticamente painéis do Azure | Microsoft Docs
description: Você pode usar um painel no portal do Azure como um modelo para criar programaticamente painéis do Azure. Inclui referência JSON.
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: mtillman
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/01/2017
ms.author: mblythe
ms.openlocfilehash: 498e0255cfa289f7d8ccb93040980c362cf510a0
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640351"
---
# <a name="programmatically-create-azure-dashboards"></a>Criar programaticamente painéis do Azure

Este documento orienta o processo de criação e publicação programaticamente de painéis do Azure. O painel mostrado abaixo é referenciado em todo o documento.

![dashboard de exemplo](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Visão geral

Os painéis compartilhados no Azure são [recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) assim como as máquinas virtuais e as contas de armazenamento.  Portanto, eles podem ser gerenciados programaticamente por meio das [APIs REST Azure Resource Manager](/rest/api/), [CLI do Azure](https://docs.microsoft.com/cli/azure), [Azure PowerShell comandos](https://docs.microsoft.com/powershell/azure/get-started-azureps)e muitos recursos de [portal do Azure](https://portal.azure.com) se baseiam nessas APIs para facilitar o gerenciamento de recursos.  

Cada uma dessas APIs e ferramentas oferece maneiras de criar, listar, recuperar, modificar e excluir recursos.  Como os painéis são recursos, você pode escolher sua API/ferramenta favorita a ser usada.

Independentemente de qual ferramenta você usa, você precisa construir uma representação JSON do seu objeto Dashboard antes de poder chamar qualquer API de criação de recursos. Este objeto contém informações sobre as partes (também conhecido como blocos) no painel. Ele inclui tamanhos, posições, recursos aos quais eles estão vinculados e personalizações de usuário.

A maneira mais prática de criar esse documento JSON é usar [o portal](https://portal.azure.com/) para adicionar e posicionar seus blocos interativamente. Em seguida, você exporta o JSON. Por fim, você cria um modelo a partir do resultado para uso posterior em scripts, programas e ferramentas de implantação.

## <a name="create-a-dashboard"></a>Create a dashboard (Criar um dashboard)

Para criar um novo painel, use o comando novo painel na tela principal do Portal.

![novo comando de painel](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Você pode usar a Galeria de blocos para localizar e adicionar blocos. Os blocos são adicionados arrastando-os e soltando-os. Alguns blocos dão suporte ao redimensionamento por meio de uma alça de arrastar, enquanto outros dão suporte a tamanhos de correção que podem ser vistos em seu menu de contexto.

### <a name="drag-handle"></a>Alça de arrasto
![alça de arrasto](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

### <a name="fixed-sizes-via-context-menu"></a>Tamanhos fixos por meio do menu de contexto
![menu de contexto de tamanhos](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Compartilhar o painel

Depois de configurar o painel de preferência, as próximas etapas serão publicar o painel (usando o comando compartilhar) e, em seguida, usar o Gerenciador de recursos para buscar o JSON.

![comando de compartilhamento](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Clicar no comando compartilhar mostra uma caixa de diálogo que solicita que você escolha em qual assinatura e grupo de recursos publicar. Tenha em mente que [você deve ter acesso de gravação](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) à assinatura e ao grupo de recursos que você escolher.

![compartilhamento e acesso](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Buscar a representação JSON do painel

A publicação leva apenas alguns segundos.  Quando terminar, a próxima etapa será ir para o [Gerenciador de recursos](https://portal.azure.com/#blade/HubsExtension/ArmExplorerBlade) para buscar o JSON.

![procurar Gerenciador de recursos](./media/azure-portal-dashboards-create-programmatically/browse-resource-explorer.png)

No Gerenciador de recursos, navegue até a assinatura e o grupo de recursos que você escolheu. Em seguida, clique no recurso de painel publicado recentemente para revelar o JSON.

![JSON do Gerenciador de recursos](./media/azure-portal-dashboards-create-programmatically/resource-explorer-json.png)

## <a name="create-a-template-from-the-json"></a>Criar um modelo a partir do JSON

A próxima etapa é criar um modelo a partir desse JSON para que ele possa ser reutilizado programaticamente com as APIs de gerenciamento de recursos apropriadas, as ferramentas de linha de comando ou dentro do Portal.

Não é necessário compreender totalmente a estrutura JSON do painel para criar um modelo. Na maioria dos casos, você deseja preservar a estrutura e a configuração de cada bloco e, em seguida, parametrizar o conjunto de recursos do Azure ao qual eles estão apontando. Examine seu painel JSON exportado e localize todas as ocorrências de IDs de recursos do Azure. Nosso painel de exemplo tem vários blocos que apontam para uma única máquina virtual do Azure. Isso porque nosso painel só examina esse único recurso. Se você pesquisar o JSON de exemplo (incluído no final do documento) para "/subscriptions", encontrará várias ocorrências dessa ID.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Para publicar esse painel para qualquer máquina virtual no futuro, você precisará parametrizar todas as ocorrências dessa cadeia de caracteres dentro do JSON. 

Há dois tipos de APIs que criam recursos no Azure. [APIs imperativas](https://docs.microsoft.com/rest/api/resources/resources) que criam um recurso por vez e um sistema [de implantação baseado em modelo](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) que pode orquestrar a criação de vários recursos dependentes com uma única chamada à API. O segundo nativamente dá suporte à parametrização e modelagem para que possamos usá-lo em nosso exemplo.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Criar programaticamente um dashboard do seu modelo usando uma implantação de modelo

O Azure oferece a capacidade de orquestrar a implantação de vários recursos. Você cria um modelo de implantação que expressa o conjunto de recursos para implantar, bem como as relações entre eles.  O formato JSON de cada recurso é o mesmo que se você estivesse criando um por um. A diferença é que a [linguagem do modelo](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) adiciona alguns conceitos, como variáveis, parâmetros, funções básicas e muito mais. Essa sintaxe estendida só tem suporte no contexto de uma implantação de modelo e não funciona se usada com as APIs imperativas discutidas anteriormente.

Se você estiver passando por essa rota, a parametrização deverá ser feita usando a sintaxe de parâmetro do modelo.  Você substitui todas as instâncias da ID de recurso que encontramos anteriormente, conforme mostrado aqui.

### <a name="example-json-property-with-hard-coded-resource-id"></a>Exemplo de propriedade JSON com ID de recurso embutido em código
`id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"`

### <a name="example-json-property-converted-to-a-parameterized-version-based-on-template-parameters"></a>Exemplo de propriedade JSON convertida em uma versão com parâmetros baseada em parâmetros de modelo

`id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"`

Você também precisa declarar alguns metadados de modelo necessários e os parâmetros na parte superior do modelo JSON como este:

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```

__Você pode ver o modelo completo e funcional no final deste documento.__

Depois de criar seu modelo, você pode implantá-lo usando as [APIs REST](https://docs.microsoft.com/rest/api/resources/deployments), o [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), a [CLI do Azure](https://docs.microsoft.com/cli/azure/group/deployment#az-group-deployment-create)ou a [página de implantação de modelo do portal](https://portal.azure.com/#create/Microsoft.Template).

Aqui estão duas versões do nosso exemplo de JSON do Dashboard. A primeira é a versão que exportamos do portal que já estava associada a um recurso. A segunda é a versão de modelo que pode ser vinculada programaticamente a qualquer VM e implantada usando Azure Resource Manager.

## <a name="json-representation-of-our-example-dashboard-before-templating"></a>Representação JSON do nosso painel de exemplo (antes dos modelos)

Isso é o que você pode esperar ver se seguir as instruções anteriores para buscar a representação JSON de um dashboard que já está implantado. Observe os identificadores de recursos embutidos em código que mostram que esse painel está apontando para uma máquina virtual específica do Azure.

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
        "metadata": { }
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

### <a name="template-representation-of-our-example-dashboard"></a>Representação de modelo do nosso painel de exemplo

A versão de modelo do painel definiu três parâmetros chamados __virtualMachineName__, __virtualMachineResourceGroup__e __dashboardname__.  Os parâmetros permitem que você aponte esse painel em uma máquina virtual do Azure diferente toda vez que implantar. As IDs parametrizadas são realçadas para mostrar que esse painel pode ser configurado e implantado programaticamente para apontar para qualquer máquina virtual do Azure. A maneira mais fácil de testar esse recurso é copiar o modelo a seguir e colá-lo na [página de implantação de modelo do portal do Azure](https://portal.azure.com/#create/Microsoft.Template). 

Este exemplo implanta um Dashboard por si só, mas a linguagem do modelo permite que você implante vários recursos e agrupe um ou mais dashboards ao mesmo lado. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}


```
