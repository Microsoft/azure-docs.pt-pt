---
title: Escalar verticalmente unidades de produtividade automaticamente – hubs de eventos do Azure | Microsoft Docs
description: Habilite o inflar automaticamente em um namespace para dimensionar automaticamente as unidades de produtividade.
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
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264469"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Dimensionar automaticamente unidades de produtividade dos hubs de eventos do Azure
Os hubs de eventos do Azure são uma plataforma de streaming de dados altamente escalonável. Dessa forma, o uso dos hubs de eventos geralmente aumenta depois de começar a usar o serviço. Esse uso exige o aumento das unidades de [produtividade](event-hubs-scalability.md#throughput-units) predeterminadas para dimensionar os hubs de eventos e lidar com taxas de transferência maiores. O recurso de **inflar** automaticamente dos hubs de eventos escala verticalmente aumentando o número de unidades de produtividade para atender às necessidades de uso. O aumento das unidades de produtividade impede cenários de limitação, em que:

* As taxas de entrada de dados excedem as unidades de produtividade definidas.
* As taxas de solicitação de saída de dados excederam as unidades de produtividade definidas.

O serviço de hubs de eventos aumenta a taxa de transferência quando o carregamento aumenta além do limite mínimo, sem nenhuma solicitação falhando com erros de ServerBusy.

## <a name="how-auto-inflate-works"></a>Como o inflar automaticamente funciona

O tráfego dos hubs de eventos é controlado por [unidades de produtividade](event-hubs-scalability.md#throughput-units). Uma única unidade de produtividade permite 1 MB por segundo de entrada e duas vezes essa quantidade de egresso. Os hubs de eventos padrão podem ser configurados com 1-20 unidades de produtividade. O inflar automaticamente permite iniciar pequeno com as unidades de produtividade mínimas necessárias que você escolher. Em seguida, o recurso é dimensionado automaticamente para o limite máximo de unidades de produtividade que você precisa, dependendo do aumento no tráfego. O inflar automaticamente oferece os seguintes benefícios:

- Um mecanismo de dimensionamento eficiente para começar pequeno e escalar verticalmente conforme o crescimento.
- Dimensionar automaticamente para o limite superior especificado sem limitação de problemas.
- Mais controle sobre o dimensionamento, pois você controla quando e quanto dimensionar.

## <a name="enable-auto-inflate-on-a-namespace"></a>Habilitar o inflar automaticamente em um namespace

Você pode habilitar ou desabilitar o inflar automaticamente em um namespace de hubs de eventos de camada Standard usando um dos seguintes métodos:

- O [portal do Azure](https://portal.azure.com).
- Um [modelo de Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate).

> [!NOTE]
> Os namespaces de hubs de eventos de camada básica não dão suporte ao inflar automaticamente.

### <a name="enable-auto-inflate-through-the-portal"></a>Habilitar o inflar automaticamente por meio do portal


#### <a name="enable-at-the-time-of-creation"></a>Habilitar no momento da criação 
Você pode habilitar o recurso de inflar automaticamente **ao criar um namespace de hubs de eventos**:
 
![Habilitar o inflar automaticamente no momento da criação do hub de eventos](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Com essa opção habilitada, você pode começar pequeno com suas unidades de produtividade e escalar verticalmente conforme seu uso precisa aumentar. O limite superior para inflação não afeta imediatamente os preços, o que depende do número de unidades de taxa de transferência usadas por hora.

#### <a name="enable-auto-inflate-for-an-existing-event-hub"></a>Habilitar o inflar automaticamente para um hub de eventos existente
Você também pode habilitar o recurso de inflar automaticamente e modificar suas configurações usando as seguintes instruções: 
 
1. Na página **namespace de hubs de eventos** , selecione **desabilitado** em **unidades de produtividade de inflar automaticamente**.  

    ![Selecionar unidades de produtividade na página namespace de hubs de eventos](./media/event-hubs-auto-inflate/select-throughput-units.png)
2. Na página **configurações de escala** , marque a caixa de seleção **habilitar** (se o recurso de dimensionamento automático não estiver habilitado).

    ![Selecione habilitar](./media/event-hubs-auto-inflate/scale-settings.png)
3. Insira o número **máximo** de unidades de produtividade ou use a barra de rolagem para definir o valor. 
4. adicional Atualize o número **mínimo** de unidades de produtividade na parte superior desta página. 


> [!NOTE]
> Quando você aplica a configuração de inflar automaticamente para aumentar as unidades de produtividade, o serviço de hubs de eventos emite logs de diagnóstico que fornecem informações sobre por que e quando a taxa de transferência aumentou. Para habilitar o log de diagnóstico para um hub de eventos, selecione **configurações de diagnóstico** no menu à esquerda da página Hub de eventos na portal do Azure. Para obter mais informações, consulte [Configurar Logs de diagnóstico para um hub de eventos do Azure](event-hubs-diagnostic-logs.md). 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Habilitar o inflar automaticamente usando um modelo de Azure Resource Manager

Você pode habilitar o inflar automaticamente durante uma implantação de modelo de Azure Resource Manager. Por exemplo, defina a propriedade `isAutoInflateEnabled` como **true** e defina `maximumThroughputUnits` como 10. Por exemplo:

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

Para obter o modelo completo, confira [criar o namespace de hubs de eventos e habilitar](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) o modelo de inflar no github.


## <a name="next-steps"></a>Passos seguintes

Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Event Hubs Overview (Descrição Geral dos Hubs de Eventos)](event-hubs-what-is-event-hubs.md)

