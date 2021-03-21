---
title: Dimensionar automaticamente as unidades de produção - Azure Event Hubs | Microsoft Docs
description: Ativar automaticamente a insuflada num espaço de nome para aumentar automaticamente as unidades de produção.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1be564472011622b71b3066495748dfdbe6cc791
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020811"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Dimensionar automaticamente unidades de produção de Hubs de Eventos Azure
O Azure Event Hubs é uma plataforma de streaming de dados altamente escalável. Como tal, o uso do Event Hubs aumenta frequentemente depois de começar a usar o serviço. Tal utilização requer o aumento das unidades de [produção](event-hubs-scalability.md#throughput-units) pré-determinadas para escalar os Centros de Eventos e lidar com taxas de transferência maiores. A função **de insuflação automática** dos Centros de Eventos aumenta automaticamente aumentando o número de unidades de produção, para satisfazer as necessidades de utilização. O aumento das unidades de produção impede cenários de estrangulamento, nos quais:

* As taxas de entrada de dados excedem as unidades de produção definidas.
* As taxas de pedido de saída de dados excedem as unidades de produção definidas.

O serviço Event Hubs aumenta a produção quando a carga aumenta para além do limiar mínimo, sem que quaisquer pedidos falhem com erros do ServerBusy.

## <a name="how-auto-inflate-works"></a>Como funciona o auto-insuflado

O tráfego do Event Hubs é controlado por [unidades de produção.](event-hubs-scalability.md#throughput-units) Uma única unidade de produção permite 1 MB por segundo de entrada e o dobro dessa quantidade de saída. Os centros de eventos padrão podem ser configurados com unidades de produção de 1-20. A insuflada automática permite-lhe iniciar pequenas com as unidades de produção mínimas necessárias que escolher. A funcionalidade então escala automaticamente para o limite máximo de unidades de produção que você precisa, dependendo do aumento do seu tráfego. A auto-inflação proporciona os seguintes benefícios:

- Um mecanismo de escala eficiente para iniciar uma pequena e escalar à medida que cresce.
- Dimensione automaticamente para o limite superior especificado sem problemas de estrangulamento.
- Mais controlo sobre a escala, porque controlas quando e quanto à escala.

## <a name="enable-auto-inflate-on-a-namespace"></a>Ativar automaticamente num espaço com nomes

Pode ativar ou desativar automaticamente um espaço de nomes Standard Tier Event Hubs utilizando qualquer um dos seguintes métodos:

- O [portal Azure.](https://portal.azure.com)
- Um [modelo de gestor de recursos Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate).

> [!NOTE]
> Os espaços de nomes de centros de eventos de nível básico não suportam a insuflação automática.

### <a name="enable-auto-inflate-through-the-portal"></a>Ativar automaticamente através do portal


#### <a name="enable-at-the-time-of-creation"></a>Ativar no momento da criação 
Pode ativar a função Desinflação Automática **ao criar um espaço de nomes de Centros de Eventos:**
 
![Permitir a insuflar automática na criação do hub do evento](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Com esta opção ativada, pode iniciar-se pequena com as suas unidades de produção e aumentar à medida que as suas necessidades de utilização aumentam. O limite máximo para a inflação não afeta imediatamente os preços, o que depende do número de unidades de produção utilizadas por hora.

#### <a name="enable-auto-inflate-for-an-existing-event-hub"></a>Ative a auto-insuflada para um centro de eventos existente
Também pode ativar a função de insuflado automático e modificar as suas definições utilizando as seguintes instruções: 
 
1. Na página **'Event Hubs Namespace',** selecione **Desativado** em **unidades de produção de insuflação automática**.  

    ![Selecione unidades de produção na página de espaço de nomes do Event Hubs](./media/event-hubs-auto-inflate/select-throughput-units.png)
2. Na página Definições de **Escala,** selecione a caixa de verificação para **Ativar** (se a função de escala automática não estiver ativada).

    ![Selecione Ativar](./media/event-hubs-auto-inflate/scale-settings.png)
3. Introduza o número **máximo** de unidades de produção ou utilize a barra de deslocação para definir o valor. 
4. (opcional) Atualize o número **mínimo** de unidades de produção no topo desta página. 


> [!NOTE]
> Quando aplica a configuração de insuflado automático para aumentar as unidades de produção, o serviço Event Hubs emite registos de diagnóstico que lhe dão informações sobre o porquê e quando a produção aumentou. Para ativar a sessão de diagnóstico de um centro de **eventos,** selecione definições de Diagnóstico no menu esquerdo na página 'Event Hub' no portal Azure. Para obter mais informações, consulte [configurar registos de diagnóstico para um centro de eventos Azure](event-hubs-diagnostic-logs.md). 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Ativar o Auto-Insuflado utilizando um modelo de Gestor de Recursos Azure

Pode ativar a insuflada automática durante a implementação do modelo do Azure Resource Manager. Por exemplo, definir a `isAutoInflateEnabled` propriedade para **verdadeiro** e definido `maximumThroughputUnits` para 10. Por exemplo:

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

Para o modelo completo, consulte o espaço de [nomes Create Event Hubs e ative o modelo de insuflação](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) no GitHub.


## <a name="next-steps"></a>Passos seguintes

Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Event Hubs](./event-hubs-about.md)
