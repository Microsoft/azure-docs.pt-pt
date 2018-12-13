---
title: Dimensionar automaticamente unidades de débito - Event Hubs do Azure | Documentos da Microsoft
description: Ative ampliação automática num espaço de nomes para a dimensionar automaticamente unidades de débito.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: cf0c7a5339d2880bbed01fc95b54038d15f1c2ce
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53083539"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Dimensionar automaticamente unidades de débito dos Hubs de eventos do Azure
Os Hubs de eventos do Azure é uma plataforma de transmissão em fluxo de dados altamente dimensionável. Como tal, a utilização de Hubs de eventos aumenta, muitas vezes, depois de começar a utilizar o serviço. Essa utilização requer aumentar o predeterminado [unidades de débito](event-hubs-features.md#throughput-units) para dimensionar os Hubs de eventos e processar maiores taxas de transferência. O **ampliação automática** funcionalidade dos Hubs de eventos de dimensionamento automaticamente do aumento do número de unidades de débito, para utilização de satisfazer as necessidades. Aumentar as unidades de débito impede cenários, no qual de limitação:

* As taxas de entrada de dados excederem as unidades de débito do conjunto.
* Taxas de pedidos de saída de dados excederem as unidades de débito do conjunto.

O serviço de Hubs de eventos aumenta o débito, quando a carga aumenta acima do limiar mínimo, sem quaisquer pedidos a falhar com erros de ServerBusy.

## <a name="how-auto-inflate-works"></a>Como funciona a ampliação automática

Tráfego de Hubs de eventos é controlado pelas [unidades de débito](event-hubs-features.md#throughput-units). Uma única unidade de débito permite que 1 MB por segundo de entrada e de duas vezes essa quantidade de saída. Os hubs de eventos Standard podem ser configurados com as unidades de débito de 1 a 20. Ampliação automática permite que comece por algo pequeno com as unidades de débito necessário mínimo que escolher. A funcionalidade, em seguida, pode ser dimensionada automaticamente para o limite máximo de unidades de débito que precisar, consoante o aumento no tráfego. Ampliação automática fornece as seguintes vantagens:

- Um mecanismo de dimensionamento eficiente começar aos poucos e aumentar verticalmente à medida que cresce.
- Dimensione automaticamente para o limite superior especificado sem problemas de limitação.
- Mais controlam sobre o dimensionamento, uma vez que controla e quanto à escala.

## <a name="enable-auto-inflate-on-a-namespace"></a>Ativar ampliação automática num espaço de nomes

Pode ativar ou desativar a ampliação automática num espaço de nomes de Hubs de eventos, utilizando qualquer um dos seguintes métodos:

- O [portal do Azure](https://portal.azure.com).
- Uma [modelo Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate).

### <a name="enable-auto-inflate-through-the-portal"></a>Ativar ampliação automática através do portal

Pode ativar a funcionalidade de ampliação automática durante a criação de um espaço de nomes de Hubs de eventos:
 
![Ativar automaticamente aumente a criação de hub de eventos de tempo](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Com esta opção ativada, pode começar com pouco com as unidades de débito e aumentar verticalmente, conforme a sua utilização precisa aumentar. O limite superior inflação não afetam imediatamente preços, que depende do número de unidades de débito utilizadas por hora.

Também pode ativar a ampliação automática com o **dimensionamento** opção no painel de definições no portal do:
 
![Ativar automaticamente ampliação usando a opção de dimensionamento](./media/event-hubs-auto-inflate/event-hubs-auto-inflate2.png)


> [!NOTE]
> Quando aplica a configuração para aumentar as unidades de débito de ampliação automática, o serviço de Hubs de eventos emite os registos de diagnóstico que dão-lhe informações sobre por que e quando o débito aumentado. Para ativar o registo de diagnósticos para um hub de eventos, selecione **das definições de diagnóstico** no menu à esquerda na página do Hub de eventos no portal do Azure. Para obter mais informações, consulte [configurar registos de diagnóstico para um hub de eventos do Azure](event-hubs-diagnostic-logs.md). 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Ativar a ampliação automática através de um modelo Azure Resource Manager

Pode ativar ampliação automática durante uma implementação de modelo do Azure Resource Manager. Por exemplo, definir o `isAutoInflateEnabled` propriedade **verdadeira** e defina `maximumThroughputUnits` para 10. Por exemplo:

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

Para o modelo completo, consulte a [espaço de nomes de Hubs de eventos de criar e ativar ampliação](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) modelo no GitHub.


## <a name="next-steps"></a>Passos Seguintes

Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)

