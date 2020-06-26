---
title: Definir alertas para métricas de grelha de eventos Azure e operações de log de atividade
description: Este artigo descreve como criar alertas nas métricas da Azure Event Grid e nas operações de registo de atividades.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/25/2020
ms.author: spelluru
ms.openlocfilehash: 488d3025f279916cb98e75f3f8db56fdc9d6d1de
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392970"
---
# <a name="set-alerts-on-azure-event-grid-metrics-and-activity-logs"></a>Definir alertas em métricas de grelha de evento azure e registos de atividade
Este artigo descreve como criar alertas nas métricas da Azure Event Grid e nas operações de registo de atividades. 

## <a name="create-alerts-on-dead-lettered-events"></a>Criar alertas sobre eventos com letras mortas
O procedimento a seguir mostra como criar um alerta sobre a métrica **de eventos com letras mortas** para um tópico personalizado. Neste exemplo, um e-mail é enviado ao proprietário do grupo de recursos Azure quando o evento com letras mortas conta para um tópico que vai acima de 10. 

1. Na página **Tópico da Grelha de Evento** para o seu tópico, selecione **Alertas** no menu esquerdo e, em seguida, selecione **+Nova regra de alerta**. 

    :::image type="content" source="./media/monitor-event-delivery/new-alert-button.png" alt-text="Página de alertas - Novo botão de regra de alerta":::
2. Na página **'Criar' regra de alerta,** verifique se o seu tópico está selecionado para o recurso. Em seguida, clique **em Selecionar a condição**. 

    :::image type="content" source="./media/monitor-event-delivery/alert-select-condition.png" alt-text="Página de alertas - selecione condição":::    
3. Na página lógica de **sinal de configuração,** siga estes passos:
    1. Selecione uma métrica ou uma entrada de registo de atividade. Neste exemplo, é selecionado **o Dead Lettered Events.** 

        :::image type="content" source="./media/monitor-event-delivery/select-dead-lettered-events.png" alt-text="Selecione eventos com letras mortas":::        
    2. Selecione dimensões (opcional). 
        
        :::image type="content" source="./media/monitor-event-delivery/configure-signal-logic.png" alt-text="Configurar lógica de sinal":::        
    3. Rola para baixo. Na secção **lógica alerta,** selecione um **Operador,** **tipo de agregação,** e introduza um **valor Threshold**, e selecione **Fazer**. Neste exemplo, um alerta é desencadeado quando o número total de eventos mortos é superior a 10. 
    
        :::image type="content" source="./media/monitor-event-delivery/alert-logic.png" alt-text="Lógica de alerta":::                
4. De volta à página **de regra de alerta Criar,** clique em Selecionar grupo de **ação**.

    :::image type="content" source="./media/monitor-event-delivery/select-action-group-button.png" alt-text="Selecione botão de grupo de ação":::
5. Selecione **Criar grupo de ação** na barra de ferramentas para criar um novo grupo de ação. Também pode selecionar um grupo de ação existente.        
6. Na página do **grupo de ação Add,** siga estes passos:
    1. Insira um **nome para o grupo de ação**.
    1. Insira um **nome curto** para o grupo de ação.
    1. Selecione uma **subscrição Azure** na qual pretende criar o grupo de ação.
    1. Selecione o **grupo de recursos Azure** no qual pretende criar o grupo de ação.
    1. Insira um **nome para a ação.** 
    1. Selecione o **tipo de ação**. Neste exemplo, é selecionada **a Função de Gestor de Recursos do Email Azure,** especificamente a função **de Proprietário.** 
    1. Selecione **OK** para fechar a página. 
    
        :::image type="content" source="./media/monitor-event-delivery/add-action-group-page.png" alt-text="Adicionar página de grupo de ação":::                   
7. Volte à página **de regra de alerta Criar,** insira um nome para a regra de alerta e, em seguida, selecione Criar a regra de **alerta**.

    :::image type="content" source="./media/monitor-event-delivery/alert-rule-name.png" alt-text="Nome da regra de alerta":::  
8. Agora, na página **alertas** do tópico, vê-se um link para gerir as regras de alerta se ainda não houver alertas. Se houver alertas, selecione **as regras de alerta do Gestor** na barra de ferramentas.  

    :::image type="content" source="./media/monitor-event-delivery/manage-alert-rules.png" alt-text="Gerir alertas":::

## <a name="create-alerts-on-other-metrics-or-activity-log-operations"></a>Criar alertas sobre outras métricas ou operações de registo de atividades
A secção anterior mostrou-lhe como criar alertas sobre eventos com letras mortas. Os passos para criar alertas em outras métricas ou operações de registo de atividades são semelhantes. 

Por exemplo, para criar um alerta num evento de falha de entrega, selecione **Eventos falhados** de Entrega na página lógica de **sinal de configuração.** 

:::image type="content" source="./media/set-alerts/delivery-failed-events.png" alt-text="Selecione eventos falhados de entrega":::


## <a name="create-alerts-using-the-metrics-page"></a>Criar alertas utilizando a página Métricas
Também pode criar alertas utilizando a página **Métricas.** Os degraus são semelhantes. 

:::image type="content" source="./media/monitor-event-delivery/metric-page-create-alert-button.png" alt-text="Página de métricas - Criar botão de alerta":::   
    

> [!NOTE]
> Este artigo não cobre todos os diferentes passos e combinações que pode usar para criar um alerta. Para obter uma visão geral dos alertas, consulte [a visão geral dos Alertas.](../azure-monitor/platform/alerts-metric.md)

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre entrega de eventos e retrórias, [entrega e redação de mensagens de Event Grid.](delivery-and-retry.md)
* Para obter uma introdução ao Event Grid, veja [Sobre o Azure Event Grid](overview.md).
* Para começar rapidamente a utilizar a Grade de Eventos, consulte [Criar e encaminhar eventos personalizados com a Azure Event Grid](custom-event-quickstart.md).
