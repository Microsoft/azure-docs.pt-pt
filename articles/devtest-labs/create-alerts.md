---
title: Criar alertas de registo de atividades para laboratórios em Azure DevTest Labs
description: Este artigo fornece medidas para criar alertas de registo de atividade para laboratório em Azure DevTest Labs.
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: f774e3291961c58f55a9ed24026535e076235b98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100588770"
---
# <a name="create-activity-log-alerts-for-labs-in-azure-devtest-labs"></a>Criar alertas de registo de atividades para laboratórios em Azure DevTest Labs
Este artigo explica como criar alertas de registo de atividade para laboratórios em Azure DevTest Labs (por exemplo: quando um VM é criado ou quando um VM é eliminado).

## <a name="create-alerts"></a>Criar alertas
Neste exemplo, cria-se um alerta para todas as operações administrativas num laboratório com uma ação que envia um e-mail aos proprietários de assinaturas. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Na barra de pesquisa do portal Azure, escreva **Monitor** e, em seguida, selecione **Monitor** na lista de resultados. 

    :::image type="content" source="./media/activity-logs/search-monitor.png" alt-text="Pesquisa rumo ao Monitor":::        
1. Selecione **Alertas** no menu esquerdo e, em seguida, selecione **Nova regra de alerta** na barra de ferramentas. 

    :::image type="content" source="./media/activity-logs/alerts-page.png" alt-text="Página de alertas":::    
1. Na página **'Criar regra de alerta',** clique **em Selecionar o recurso**. 

    :::image type="content" source="./media/activity-logs/select-resource-link.png" alt-text="Selecione recurso para o alerta":::        
1. Selecione **DevTest Labs** for **Filter por tipo de recurso,** selecione o seu laboratório na lista e, em seguida, selecione **'Fazer'**

    :::image type="content" source="./media/activity-logs/select-lab-resource.png" alt-text="Selecione o seu laboratório como recurso":::
1. De volta à página **de regra de alerta Criar,** clique em Selecionar a **condição**. 

    :::image type="content" source="./media/activity-logs/select-condition-link.png" alt-text="Selecione link de condição":::    
1. Na página lógica de **sinal de configuração,** selecione um sinal suportado pela DevTest Labs. 

    :::image type="content" source="./media/activity-logs/select-signal.png" alt-text="Selecione sinal":::
1. Filtrar por **nível de evento** (Verbose, Informational, Warning, Error, Critical, All), **status** (Failed, Started, Succeeded) e **quem iniciou** o evento. 
1. Selecione **Feito** para completar a configuração da condição. 

    :::image type="content" source="./media/activity-logs/configure-signal-logic-done.png" alt-text="Configure lógica de sinal - feito":::
1. Especificou para o âmbito (laboratório) e a condição para o alerta. Agora, tens de especificar um grupo de ação com ações a serem executadas quando a condição for cumprida. De volta à página **de regras de alerta Criar,** escolha **Selecione grupo de ação**. 

    :::image type="content" source="./media/activity-logs/select-action-group-link.png" alt-text="Selecione link de grupo de ação":::
1. **Selecione Criar ligação de grupo de ação** na barra de ferramentas. 

    :::image type="content" source="./media/activity-logs/create-action-group-link.png" alt-text="Criar ligação de grupo de ação":::
1. Na página do **grupo de ação Add,** siga estes passos:
    1. Insira um **nome** para o grupo de ação.
    1. Insira um **nome curto** para o grupo de ação. 
    1. Selecione o **grupo de recursos** no qual deseja que o alerta seja criado. 
    1. Insira um **nome para a ação.** 
    1. Selecione o **tipo de ação** (neste exemplo, **Email Azure Resource Manager Role**). 

        :::image type="content" source="./media/activity-logs/add-action-group.png" alt-text="Adicionar página de grupo de ação":::
    1. Na página **'Email Azure Resource Manager Role',** selecione a função. Neste exemplo, é **Proprietário.** Em seguida, selecione **OK**. 

        :::image type="content" source="./media/activity-logs/select-role.png" alt-text="Selecionar função":::            
    1. Selecione **OK** na página do **grupo de ação Add.** 
1. Agora, na página **de regra de alerta Criar,** insira um nome para a regra de alerta e, em seguida, selecione **OK**. 

    :::image type="content" source="./media/activity-logs/create-alert-rule-done.png" alt-text="Criar regra de alerta - feito":::

## <a name="view-alerts"></a>Ver alertas 
1. Verá alertas nos **Alertas** para todas as operações administrativas (neste exemplo). Os alertas podem demorar algum tempo a aparecer. 

    :::image type="content" source="./media/activity-logs/alerts.png" alt-text="A captura de ecrãs apresenta alertas no Painel de Instrumentos.":::
1. Se selecionar o número numa coluna (por exemplo: **Alertas totais),** vê os alertas que foram levantados. 

    :::image type="content" source="./media/activity-logs/all-alerts.png" alt-text="Todos os alertas":::
1. Se selecionar um alerta, verá detalhes sobre o mesmo. 

    :::image type="content" source="./media/activity-logs/alert-details.png" alt-text="Detalhes do alerta":::
1. Neste exemplo, também recebe um e-mail com conteúdo como mostrado no seguinte exemplo: 

    :::image type="content" source="./media/activity-logs/alert-email.png" alt-text="E-mail de alerta":::

## <a name="next-steps"></a>Passos seguintes
- Para saber mais sobre a criação de grupos de ação utilizando diferentes tipos de ação, consulte [Criar e gerir grupos de ação no portal Azure.](../azure-monitor/alerts/action-groups.md)
- Para saber mais sobre os registos de atividades, consulte  [o Registo de Atividades do Azure](../azure-monitor/essentials/activity-log.md).
- Para saber sobre a definição de alertas nos registos de atividade, consulte [Alertas no registo de atividades](../azure-monitor/alerts/activity-log-alerts.md).

