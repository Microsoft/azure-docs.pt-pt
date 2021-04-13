---
title: Configure alertas e trabalhe com métricas na Azure VMware Solution
description: Saiba como usar alertas para receber notificações. Também aprenda a trabalhar com métricas para obter informações mais profundas sobre a sua nuvem privada Azure VMware Solution.
ms.topic: how-to
ms.date: 04/02/2021
ms.openlocfilehash: 486f25eba017b2d4e37c0796909a0d26adee6ba8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309633"
---
# <a name="configure-azure-alerts-in-azure-vmware-solution"></a>Configure Alertas Azure em Solução VMware Azure 

Neste artigo, você vai aprender a configurar [grupos de ação Azure](/azure/azure-monitor/alerts/action-groups) no [Microsoft Azure Alerts](/azure/azure-monitor/alerts/alerts-overview) para receber notificações de eventos desencadeados que você define. Você também vai aprender sobre o uso [de Azure Monitor Metrics](/azure/azure-monitor/essentials/data-platform-metrics) para obter informações mais profundas sobre a sua nuvem privada Azure VMware Solution.


## <a name="supported-metrics-and-activities"></a>Métricas e atividades apoiadas

As seguintes métricas são visíveis através das Métricas do Monitor Azure.

| **Nome do sinal**                                                         | **Tipo de sinal** | **Serviço de monitorização** |
|-------------------------------------------------------------------------|-----------------|---------------------|
| Capacidade total do disco de datastore                                           | Metric          | Plataforma            |
| Disco percentual de datastore utilizado                                          | Metric          | Plataforma            |
| Percentagem da CPU                                                          | Metric          | Plataforma            |
| Memória média eficaz                                                | Metric          | Plataforma            |
| Despesa média de memória                                                 | Metric          | Plataforma            |
| Memória total média                                                    | Metric          | Plataforma            |
| Uso médio da memória                                                    | Metric          | Plataforma            |
| Disco de datastore utilizado                                                     | Metric          | Plataforma            |
| Todas as operações administrativas                                           | Registo de Atividades    | Administrativo      |
| Registe o fornecedor de recursos Microsoft.AVS. (Microsoft.AVS/privateClouds) | Registo de Atividades    | Administrativo      |
| Criar ou atualizar um PrivateCloud. (Microsoft.AVS/privateClouds)          | Registo de Atividades    | Administrativo      |
| Apague um PrivateCloud. (Microsoft.AVS/privateClouds)                    | Registo de Atividades    | Administrativo      |

## <a name="configure-an-alert-rule"></a>Configure uma regra de alerta
1. A partir da sua nuvem privada Azure VMware Solution, selecione Alertas **de Monitorização**  >  e, em seguida, Nova regra **de alerta**.
 
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png" alt-text="Screenshot que mostra onde configurar uma regra de alerta na sua nuvem privada Azure VMware Solution." lightbox="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png":::

   Abre-se um novo ecrã de configuração onde:
   - Definir o Âmbito
   - Configurar uma condição
   - Criar o Grupo de Ação
   - Definir os detalhes da regra de alerta
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-alert-rule-details.png" alt-text="Screenshot que mostra a janela de regra de alerta Criar." lightbox="media/configure-alerts-for-azure-vmware-solution/create-alert-rule-details.png":::

1. No **âmbito do Scope,** selecione o recurso-alvo que pretende monitorizar. Por predefinição, foi definida a nuvem privada Azure VMware Solution de onde abriu o menu Alertas.

1. Em **Condições**, **selecione Adicionar a condição**, e na janela que se abre, selecione o sinal que pretende criar para a regra de alerta. 

   No nosso exemplo, selecionámos **o disco percentual usado,** o que é relevante a partir de uma perspetiva SLA de [SÔ SLA de Solução VMware Azure.](https://aka.ms/avs/sla) 

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/configure-signal-logic-options.png" alt-text="Screenshot que mostra a janela lógica de sinal configure com nomes de sinal predefinidos."::: 

1. Defina a lógica que irá desencadear o alerta e, em seguida, selecione **Fazer**. 

   No nosso exemplo, apenas o **Limiar** e **a Frequência de avaliação** foram ajustados. 
   
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/define-alert-logic-threshold.png" alt-text="Screenshot que mostra a informação para a lógica de sinal selecionada."::: 

1. Em **Ações**, **selecione Adicionar grupos de ação**. O grupo de ação define *como* a notificação é recebida e *quem* a recebe.   Pode receber notificações por e-mail, SMS, [Azure Mobile App Push Notification](https://azure.microsoft.com/features/azure-portal/mobile-app/) ou mensagem de voz.
 
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group.png" alt-text="Screenshot que mostra os grupos de ação existentes e onde criar um novo grupo de ação.":::

1. Na janela que se abre, selecione **Criar grupo de ação**.

   >[!TIP]
   > Também pode utilizar um grupo de ação existente.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/select-action-group-for-alert-rule.png" alt-text="Screenshot que mostra os grupos de ação para selecionar para o alerta."::: 

 

 
1. Na janela que abre, no separador **Básico,** dê ao grupo de ação um nome e um nome de exibição.

1. Selecione o **separador Notificações,** selecione um Tipo e **Nome de** **Notificação.** Em seguida, selecione **OK**.

   O nosso exemplo baseia-se na notificação por e-mail.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group-notification-settings.png" alt-text="Screenshot que mostra o e-mail, mensagem SMS, push e configurações de voz para o alerta." lightbox="media/configure-alerts-for-azure-vmware-solution/create-action-group-notification-settings.png":::     

1. (Opcional) Configure as **Ações** se quiser tomar ações proativas e receber a notificação sobre o evento. Selecione um tipo de **Ação** disponível e, em seguida, selecione **Review + create**. 
   - Runbooks de Automatização
   - Funções Azure – para execução personalizada de código sem servidor dirigido a eventos
   - ITSM – integrar-se com um prestador de serviços como o ServiceNow para criar um bilhete
   - Logic App - para orquestração de fluxo de trabalho mais complexa
   - Webhooks - para desencadear um processo em outro serviço

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group-action-type.png" alt-text="Screenshot que mostra a janela do grupo de ação Create com foco no drop-down do tipo Action." lightbox="media/configure-alerts-for-azure-vmware-solution/create-action-group-action-type.png":::     

1. De acordo com os detalhes da **regra de alerta,** forneça um nome, descrição, grupo de recursos para armazenar a regra de alerta, a gravidade. Em seguida, **selecione Criar a regra de alerta**.
   
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/alert-rule-details.png" alt-text="Screenshot que mostra os detalhes da regra de alerta."::: 
 
   A regra de alerta é visível e pode ser gerida a partir do portal Azure.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png" alt-text="Screenshot que mostra a nova regra de alerta na janela Regras." lightbox="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png":::     

   Assim que uma métrica atinge o limiar tal como definido numa regra de alerta, o menu **Alertas** é atualizado e tornado visível.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png" alt-text="Screenshot que mostra o alerta depois de atingir o limiar definido." lightbox="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png":::     

   Dependendo do Grupo de Ação configurado, receberá uma notificação através do meio configurado. No nosso exemplo, configuramos o e-mail.
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/alert-notification.png" alt-text="Screenshot de um Alerta monitor Azure com a cadeia de erro, e o evento de data e hora foi desencadeado."::: 

## <a name="work-with-metrics"></a>Trabalhar com métricas

1. A partir da sua nuvem privada Azure VMware Solution, selecione   >  **Métricas** de Monitorização . Em seguida, selecione a métrica desejada a partir do drop-down.
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png" alt-text="Screenshot que mostra a janela Métricas e um foco na queda métrica." lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png":::   

1. Pode alterar os parâmetros do diagrama, como o **intervalo de tempo** ou a **granularidade do tempo.** 

   Outras opções são:
   - **Perfurar em Logs** e consultar os dados no espaço de trabalho do Log Analytics
   - **Pin este diagrama** num Painel Azure para conveniência.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png" alt-text="Screenshot que mostra o intervalo de tempo e as opções de granularidade de tempo para métrica." lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png":::  
 
 
## <a name="next-steps"></a>Passos seguintes

Agora que configuraste uma regra de alerta para a tua nuvem privada Azure VMware Solution, talvez queiras saber ainda mais sobre:
- [Métricas do Azure Monitor](/azure/azure-monitor/essentials/data-platform-metrics)
- [Alertas do Azure Monitor](/azure/azure-monitor/alerts/alerts-overview)
- [Grupos de ação Azure](/azure/azure-monitor/alerts/action-groups)

Também pode continuar com um dos outros guias [Azure VMware Solution](index.yml) how-to.





