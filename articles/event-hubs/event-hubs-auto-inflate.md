---
title: Escala automaticamente as unidades de produção - Azure Event Hubs / Microsoft Docs
description: Ativar automaticamente insuflar-se num espaço de nome para aumentar automaticamente as unidades de produção.
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
ms.openlocfilehash: dc6edaebebe89b6d4a35ada58d40795f86a935d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72264469"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Escala automaticamente as unidades de produção do Azure Event Hubs
O Azure Event Hubs é uma plataforma de streaming de dados altamente escalável. Como tal, o uso de Centros de Eventos aumenta frequentemente após começar a usar o serviço. Esta utilização requer o aumento das [unidades](event-hubs-scalability.md#throughput-units) de produção pré-determinadas para escalar os Centros de Eventos e lidar com taxas de transferência maiores. A função **de auto-insuflar** os Centros de Eventos aumenta automaticamente aumentando o número de unidades de produção, para satisfazer as necessidades de utilização. O aumento das unidades de entrada impede cenários de estrangulamento, nos quais:

* As taxas de ingresso de dados excedem as unidades de entrada definidas.
* As taxas de pedido de saída de dados excedem as unidades de entrada definidas.

O serviço Event Hubs aumenta a produção quando a carga aumenta para além do limiar mínimo, sem que quaisquer pedidos falhem com erros do ServerBusy.

## <a name="how-auto-inflate-works"></a>Como funciona o auto-insuflador

O tráfego de Hubs de Eventos é controlado por [unidades](event-hubs-scalability.md#throughput-units)de produção . Uma única unidade de entrada permite 1 MB por segundo de entrada e o dobro dessa quantidade de saída. Os centros de eventos padrão podem ser configurados com unidades de entrada 1-20. A insuflação automática permite-lhe iniciar-se em pequena si com as unidades de entrada mínimas exigidas que escolher. A funcionalidade eleva-se automaticamente ao limite máximo das unidades de entrada de que necessita, dependendo do aumento do tráfego. A auto-inflação proporciona os seguintes benefícios:

- Um mecanismo de escala eficiente para iniciar pequena e escalar à medida que cresce.
- Escala automaticamente para o limite superior especificado sem problemas de estrangulamento.
- Mais controlo sobre a escala, porque controla quando e quanto escalar.

## <a name="enable-auto-inflate-on-a-namespace"></a>Ativar automaticamente insuflar num espaço de nome

Pode ativar ou desativar automaticamente um espaço de nome standard de centros de eventos utilizando qualquer um dos seguintes métodos:

- O [portal Azure.](https://portal.azure.com)
- Um modelo de Gestor de [Recursos Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate).

> [!NOTE]
> Os espaços de nome de centros de eventos de nível básico não suportam o auto-insuflador.

### <a name="enable-auto-inflate-through-the-portal"></a>Ativar automaticamente insuflar através do portal


#### <a name="enable-at-the-time-of-creation"></a>Ativar no momento da criação 
Pode ativar a função de auto-insuflar ao criar um espaço de nome dos Centros de **Eventos:**
 
![Ativar automaticamente inflação na criação do centro de eventos de tempo](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Com esta opção ativada, pode começar a ser pequeno com as suas unidades de entrada e aumentar à medida que a sua utilização precisa aumentar. O limite máximo para a inflação não afeta imediatamente os preços, o que depende do número de unidades de entrada utilizadas por hora.

#### <a name="enable-auto-inflate-for-an-existing-event-hub"></a>Ativar automaticamente insuflar para um centro de eventos existente
Também pode ativar a função de auto-inflação e modificar as suas definições utilizando as seguintes instruções: 
 
1. Na página Namespace do **Event Hubs,** selecione **Desativado** sob **unidades de entrada de insufladoautomático**automaticamente .  

    ![Selecione unidades de entrada na página do espaço de nome do Event Hubs](./media/event-hubs-auto-inflate/select-throughput-units.png)
2. Na página Definições de **Escala,** selecione a caixa de verificação para **Ativar** (se a função de escala automática não estiver ativada).

    ![Selecionar Ativar](./media/event-hubs-auto-inflate/scale-settings.png)
3. Introduza o número **máximo** de unidades de entrada ou utilize a barra de deslocação para definir o valor. 
4. (opcional) Atualize o número **mínimo** de unidades de entrada no topo desta página. 


> [!NOTE]
> Quando aplica a configuração de auto-inflação para aumentar as unidades de produção, o serviço Event Hubs emite registos de diagnóstico que lhe dão informações sobre o porquê e quando a entrada aumentou. Para permitir o registo de diagnóstico para um centro de eventos, selecione **definições de diagnóstico** no menu esquerdo na página Do Hub de Eventos no portal Azure. Para mais informações, consulte [A configuração de registos de diagnóstico para um centro de eventos Azure](event-hubs-diagnostic-logs.md). 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Ativar o auto-insuflar usando um modelo de Gestor de Recursos Azure

Pode ativar a inflação automática durante a implementação do modelo do Gestor de Recursos Azure. Por exemplo, `isAutoInflateEnabled` coloque a propriedade `maximumThroughputUnits` em **realidade** e definida para 10. Por exemplo:

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

Para obter o modelo completo, consulte o espaço de nome sinuoso [do "Create Event Hubs" e ative](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) o modelo de insuflar no GitHub.


## <a name="next-steps"></a>Passos seguintes

Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Event Hubs](event-hubs-what-is-event-hubs.md)

