---
title: Gerencie notificações de alerta de eventos para os recursos do Azure Stack Edge Pro | Microsoft Docs
description: Descreve como usar o portal Azure para gerir alertas para eventos de dispositivos nos seus recursos Azure Stack Edge Pro.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 585343137a4a8fd8a1fb591c640e1183d71c0fd3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443102"
---
# <a name="manage-device-event-alert-notifications-on-azure-stack-edge-pro-resources"></a>Gerir notificações de alerta de eventos de dispositivos em recursos Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como criar regras de ação no portal Azure para desencadear ou suprimir notificações de alerta para eventos de dispositivos que ocorrem dentro de um grupo de recursos, uma subscrição Azure ou um recurso individual Azure Stack Edge. Este artigo aplica-se a todos os modelos de Azure Stack Edge.  

## <a name="about-action-rules"></a>Sobre regras de ação

Uma regra de ação pode desencadear ou suprimir notificações de alerta. A regra de ação é adicionada a um grupo de *ação* - um conjunto de preferências de notificação que é usada para notificar os utilizadores que precisam de agir em alertas desencadeados em diferentes contextos para um recurso ou conjunto de recursos.

Para obter mais informações sobre regras de ação, consulte [configurar uma regra de ação](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule). Para obter mais informações sobre grupos de ação, consulte [Criar e gerir grupos de ação no portal Azure.](../azure-monitor/alerts/action-groups.md)

> [!NOTE]
> O recurso de regras de ação está em pré-visualização. Alguns ecrãs e passos podem mudar à medida que o processo é refinado.


## <a name="create-an-action-rule"></a>Criar uma regra de ação

Tome os seguintes passos no portal Azure para criar uma regra de ação para o seu dispositivo Azure Stack Edge.

> [!NOTE]
> Estas medidas criam uma regra de ação que envia notificações a um grupo de ação. Para obter detalhes sobre a criação de uma regra de ação para suprimir notificações, consulte [configurar uma regra de ação](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule).

1. Vá ao dispositivo Azure Stack Edge no portal Azure e, em seguida, vá a **Monitoring > Alerts**. Selecione **Gerir ações**.

   ![Alertas de Monitorização, Gerir a visão de ações](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-01.png)

2. Selecione **regras de ação (pré-visualização)** e, em seguida, selecione **+ Nova regra de ação**.

   ![Gerir ações, opção de regras de ação](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-02.png)

3. No ecrã de **regras de ação Criar,** utilize **o Scope** para selecionar uma subscrição do Azure, um grupo de recursos ou um recurso-alvo. A regra de ação atuará em todos os alertas gerados dentro desse âmbito.

   1. Escolha **Selecione** por **Âmbito.**

      ![Selecione uma margem para uma nova regra de ação](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-01.png)

   2. Selecione a **Assinatura** para a regra de ação, filtrar opcionalmente por um tipo **de recurso.** Para filtrar os recursos do Azure Stack Edge, selecione **dispositivos Data Box Edge (dataBoxEdge)**.

      A área **de Recursos** lista os recursos disponíveis com base nas suas seleções.
  
      ![Recursos disponíveis no ecrã Select Scope](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-02.png)

   3. Selecione a caixa de verificação por cada recurso a que pretende aplicar a regra. Pode selecionar a subscrição, grupos de recursos ou recursos individuais. Em seguida, selecione **Done** (Concluído).

      ![Definições de amostra para um âmbito de regra de ação](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-03.png)

      O ecrã **de regras de ação Create** mostra o âmbito selecionado.

      ![Âmbito preenchido para uma regra de ação Azure Stack Edge](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-04.png)

4. Utilize opções **de filtro** para limitar a aplicação da regra ao subconjunto de alertas dentro do âmbito selecionado.

   1. **Selecione Adicione** para abrir o painel **de filtros Adicionar.**

      ![Opção para adicionar filtros a uma regra de ação](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-01.png)

   2. Em **Filtros,** adicione cada filtro que pretende aplicar. Para cada filtro, selecione o tipo de filtro, **Operador** e **Valor**.
   
      Para obter uma lista de opções de filtro, consulte [os critérios do Filtro](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#filter-criteria).

      Os filtros de amostra abaixo aplicam-se a todos os alertas nos níveis de Severidade 2, 3 e 4 que o serviço Monitor eleva para os recursos Azure Stack Edge.

      Quando terminar de adicionar filtros, selecione **Fazer**.
   
      ![Filtro de amostra para uma regra de ação](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-02.png)

5. No ecrã de **regras de ação Criar,** selecione **Action group** para criar uma regra que envia notificações. Em seguida, por **Ações,** escolha **Select**.

   ![Opção de grupo de ação para criar uma regra de ação que envia notificações](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-01.png)

   > [!NOTE]
   > Para criar uma regra que suprime as notificações, escolheria **supressão**. Para obter mais informações, consulte [configurar uma regra de ação.](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule)

6. Selecione o grupo de ação que pretende utilizar com esta regra de ação. Em seguida, escolha **Selecionar**. A sua nova regra de ação será adicionada às preferências de notificação do grupo de ação selecionado.

   Se precisar de criar um novo grupo de ação, selecione **+ Criar grupo de ação,** e siga os passos na [Criação de um grupo de ação utilizando o portal Azure](../azure-monitor/alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal).

   ![Selecione um grupo de ação para usar com a regra e, em seguida, escolha Selecione.](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-02.png)

7. Dê à nova regra de ação um **Nome** e **Descrição** e atribua a regra a um grupo de recursos.

9. A nova regra é ativada por defeito. Se não quiser começar a usar a regra imediatamente, selecione **Não** para **Ativar a criação de atualização de regras**.

10. Quando terminar as definições, **selecione Criar**.

    ![Definições concluídas para uma regra de ação que enviará notificações de alerta](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-completed-settings.png)

    O ecrã **de regras de Ação (Pré-visualização)** abre, mas pode não ver a sua nova regra de ação imediatamente. O foco são **todos os** grupos de recursos.

11. Para ver a sua nova regra de ação, selecione o grupo de recursos para a regra.

    ![Tela de regras de ação com a nova regra exibida](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-displayed.png)


## <a name="view-notifications"></a>Ver notificações

As notificações saem quando um novo evento desencadeia um alerta para um recurso que está dentro do âmbito de uma regra de ação.

O grupo de ação para uma regra define quem recebe uma notificação e o tipo de notificação que é enviada - e-mail, uma mensagem do Serviço de Mensagens Curtas (SMS) ou ambos.

Pode levar alguns minutos a receber notificações depois de um alerta ser desencadeado.

A notificação por e-mail será semelhante a esta.

![Amostra de notificação de e-mail para uma regra de ação](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/sample-action-rule-email-notification.png)


## <a name="next-steps"></a>Passos seguintes

<!-- - See [Create and manage action groups in the Azure portal](../azure-monitor/alerts/action-groups.md) for guidance on creating a new action group.
- See [Configure an action rule](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule) for more info about creating action rules that send or suppress alert notifications. -2 bullets referenced above. Making room for local tasks in "Next Steps." --> 
- Consulte [o Monitor do Azure Stack Edge Pro](azure-stack-edge-monitor.md) para obter informações sobre a revisão de eventos de dispositivos, estado de hardware e gráficos de métricas. 
- Consulte [o Monitor Azure](azure-stack-edge-gpu-enable-azure-monitor.md) para obter informações sobre a otimização do Monitor Azure para dispositivos GPU Azure Stack Edge Pro.
- Consulte [Criar, visualizar e gerir alertas métricos utilizando o alvo Azure Monitor Link](../azure-monitor/alerts/alerts-metric.md) para obter informações sobre a gestão de alertas individuais.