---
title: Criar programáticamente dashboards Azure
description: Utilize um dashboard no portal Azure como modelo para criar programáticamente dashboards Azure. Inclui referência JSON.
services: azure-portal
documentationcenter: ''
author: mgblythe
manager: mtillman
ms.service: azure-portal
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 12/4/2020
ms.author: mblythe
ms.openlocfilehash: 7e6819b01af3fc9357417a838fefce7f2c73dcce
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558221"
---
# <a name="programmatically-create-azure-dashboards"></a>Criar programáticamente dashboards Azure

Este artigo acompanha-o através do processo de criação e publicação programática de dashboards Azure. O painel de instrumentos apresentado abaixo é referenciado em todo o documento.

![dashboard de exemplo](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Descrição geral

Dashboards partilhados no [portal Azure](https://portal.azure.com) são [recursos](../azure-resource-manager/management/overview.md) como máquinas virtuais e contas de armazenamento. Pode gerir os recursos programáticamente utilizando os comandos [REST,](/rest/api/)Azure Resource Manager , [Azure CLI](/cli/azure)e [Azure PowerShell](/powershell/azure/get-started-azureps).

Muitas funcionalidades baseiam-se nestas APIs para facilitar a gestão de recursos. Cada uma destas APIs e ferramentas oferece formas de criar, listar, recuperar, modificar e eliminar recursos. Uma vez que os dashboards são recursos, pode escolher a sua API ou ferramenta favorita para usar.

Quaisquer que sejam as ferramentas que utilizar, para criar um dashboard programáticamente, constrói uma representação JSON do seu objeto do painel de instrumentos. Este objeto contém informações sobre os azulejos no painel de instrumentos. Inclui tamanhos, posições, recursos a que estão obrigados, e quaisquer personalizações do utilizador.

A forma mais prática de construir este documento JSON é usar o portal Azure. Pode adicionar e posicionar interativamente os seus azulejos. Em seguida, exporte o JSON e crie um modelo a partir do resultado para posterior utilização em scripts, programas e ferramentas de implementação.

## <a name="create-a-dashboard"></a>Criar um dashboard

Para criar um dashboard, selecione **Dashboard** a partir do menu do [portal Azure](https://portal.azure.com) e, em seguida, selecione **Novo painel de instrumentos**.

![novo comando do painel de instrumentos](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Utilize a galeria de azulejos para encontrar e adicionar azulejos. Os azulejos são adicionados arrastando-os e largando-os. Alguns azulejos suportam a redimensionamento utilizando uma pega de arrasto.

![arrastar o cabo para mudar de tamanho](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

Outros têm tamanhos fixos para escolher no seu menu de contexto.

![menu de contexto de tamanhos para alterar tamanho](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Partilhe o painel de instrumentos

Depois de configurar o painel de instrumentos, o próximo passo é publicar o painel de instrumentos utilizando o comando **Partilhar.**

![partilhando um dashboard](./media/azure-portal-dashboards-create-programmatically/share-command.png)

A seleção **do Share** leva-o a escolher a subscrição e o grupo de recursos para onde publicar. Deve ter acesso ao grupo de subscrição e recursos que escolher. Para obter mais informações, veja [Utilizar o portal do Azure para adicionar ou remover atribuições de funções do Azure](../role-based-access-control/role-assignments-portal.md).

![fazer alterações na partilha e no acesso](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Pegue a representação JSON do tablier

Publicar leva apenas alguns segundos. Quando estiver feito, o próximo passo é buscar o JSON usando o comando **Download.**

![baixar representação JSON](./media/azure-portal-dashboards-create-programmatically/download-command.png)

## <a name="create-a-template-from-the-json"></a>Crie um modelo a partir do JSON

O próximo passo é criar um modelo a partir deste JSON. Utilize este modelo programáticamente com as APIs de gestão de recursos apropriadas, ferramentas de linha de comando ou dentro do portal.

Não é preciso compreender completamente a estrutura JSON do dashboard para criar um modelo. Na maioria dos casos, pretende preservar a estrutura e configuração de cada azulejo. Em seguida, parametrize o conjunto de recursos Azure que os azulejos apontam. Veja o seu painel JSON exportado e encontre todas as ocorrências de IDs de recursos Azure. O nosso painel de instrumentos de exemplo tem vários azulejos que apontam para uma única máquina virtual Azure. Isso é porque o nosso painel só olha para este único recurso. Se pesquisar a amostra JSON, incluída no final do documento, para "/subscrições", encontrará várias ocorrências deste ID.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Para publicar este dashboard para qualquer máquina virtual no futuro, paramese cada ocorrência desta cadeia dentro do JSON.

Existem duas abordagens para as APIs que criam recursos em Azure:

* As APIs imperativas criam um recurso de cada vez. Para mais informações, consulte [Recursos.](/rest/api/resources/resources)
* Um sistema de implementação baseado em modelo que cria múltiplos recursos dependentes com uma única chamada API. Para obter mais informações, consulte  [implementar recursos com modelos de Gestor de Recursos e Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md).

A implementação baseada no modelo suporta a parametrização e a templário. Utilizamos esta abordagem neste artigo.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Criar programáticamente um dashboard a partir do seu modelo usando uma implementação de modelo

O Azure oferece a capacidade de orquestrar a implantação de múltiplos recursos. Cria-se um modelo de implantação que expressa o conjunto de recursos a implementar e as relações entre eles.  O formato JSON de cada recurso é o mesmo que se estivesse a criá-los um a um. A diferença é que a linguagem do modelo adiciona alguns conceitos como variáveis, parâmetros, funções básicas, e muito mais. Esta sintaxe estendida só é suportada no contexto de uma implementação do modelo. Não funciona se usado com as APIs imperativas discutidas anteriormente. Para obter mais informações, [consulte a estrutura e sintaxe dos modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/template-syntax.md).

A parametrização deve ser feita utilizando a sintaxe do parâmetro do modelo.  Você substitui todas as instâncias do ID de recursos que encontramos anteriormente, como mostrado aqui.

Propriedade exemplo JSON com ID de recursos codificados:

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

Propriedade JSON exemplo convertida para uma versão parametrizada com base em parâmetros de modelo

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

Declare metadados de modelo necessários e os parâmetros no topo do modelo JSON como este:

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
Uma vez configurado o seu modelo, desloque-o utilizando qualquer um dos seguintes métodos:

* [APIs REST](/rest/api/resources/deployments)
* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [CLI do Azure](/cli/azure/group/deployment#az-group-deployment-create)
* [A página de implementação do modelo do portal Azure](https://portal.azure.com/#create/Microsoft.Template)

Em seguida, você verá duas versões do nosso exemplo dashboard JSON. A primeira é a versão que exportamos do portal que já estava ligado a um recurso. A segunda é a versão do modelo que pode ser ligada programáticamente a qualquer máquina virtual e implementada usando o Azure Resource Manager.

### <a name="json-representation-of-our-example-dashboard-before-templating"></a>JSON representação do nosso painel de exemplo antes de templário

Este exemplo mostra o que pode esperar ver se seguiu com este artigo. As instruções exportaram a representação JSON de um painel de instrumentos que já está implantado. Os identificadores de recursos codificados mostram que este painel está a apontar para uma máquina virtual Azure específica.

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

### <a name="template-representation-of-our-example-dashboard"></a>Representação do modelo do nosso painel de exemplo

A versão do modelo do painel de instrumentos definiu três parâmetros chamados `virtualMachineName` `virtualMachineResourceGroup` , e `dashboardName` .  Os parâmetros permitem-lhe apontar este painel para uma máquina virtual Azure diferente sempre que se implanta. Este painel pode ser configurado programáticamente e implantado para apontar para qualquer máquina virtual Azure. Para testar esta função, copie o modelo a seguir e cole-o na página de implementação do [modelo do portal Azure](https://portal.azure.com/#create/Microsoft.Template).

Este exemplo implementa um dashboard por si só, mas a linguagem do modelo permite-lhe implementar vários recursos, e agregar um ou mais dashboards ao lado deles.

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

Agora que viu um exemplo de utilização de um modelo parametrizado para implantar um dashboard, pode tentar implementar o modelo utilizando os comandos [REST,](/rest/api/)Azure Resource Manager , [Azure CLI](/cli/azure)ou [Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="programmatically-create-a-dashboard-by-using-azure-cli"></a>Criar programáticamente um dashboard utilizando o Azure CLI

Prepare o seu ambiente para o Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Estes exemplos utilizam o seguinte painel de instrumentos: [portal-dashboard-template-testvm.jsem .](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json) Substitua o conteúdo em suportes angulares com os seus valores.

Executar o [painel de instrumentos do portal az criar](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_create) comando para criar um dashboard:

```azurecli
az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

Pode atualizar um dashboard utilizando o comando de atualização do painel de instrumentos do [portal Az:](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_update)

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
--input-path portal-dashboard-template-testvm.json --location centralus
```

Consulte os detalhes de um dashboard executando o comando do painel de instrumentos do [portal az:](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_show)

```azurecli
az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
```

Para ver todos os dashboards para a subscrição atual, utilize [a lista do painel de instrumentos do portal Az](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_list):

```azurecli
az portal dashboard list
```

Também pode ver todos os dashboards para um grupo de recursos:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre ambientes de trabalho, consulte [as definições e preferências do portal Manage Azure](set-preferences.md).

Para obter mais informações sobre o suporte do Azure CLI para os dashboards, consulte [o painel de instrumentos do portal Az](/cli/azure/ext/portal/portal/dashboard).
