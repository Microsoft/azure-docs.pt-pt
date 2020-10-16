---
title: Registos de atividades em Azure DevTest Labs Microsoft Docs
description: Este artigo fornece passos para visualizar registos de atividades para Azure DevTest Labs.
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: 24b5eb25f1c8c9044ad6e77545381a43a11714f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87097854"
---
# <a name="view-activity-logs-for-labs-in-azure-devtest-labs"></a>Ver registos de atividades para laboratórios em Azure DevTest Labs 
Depois de criar um ou mais laboratórios, provavelmente vai querer monitorizar como e quando os seus laboratórios são acedidos, modificados e geridos, e por quem. A Azure DevTest Labs usa o Azure Monitor, especificamente **registos de atividade,** para fornecer informações sobre estas operações contra laboratórios. 

Este artigo explica como ver registos de atividades para um laboratório em Azure DevTest Labs.

## <a name="view-activity-log-for-a-lab"></a>Ver registo de atividade para um laboratório

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** na secção **DEVOPS.** Se selecionar * (estrela) ao lado **de DevTest Labs** na secção **DEVOPS.** Esta ação adiciona **a DevTest Labs** ao menu de navegação à esquerda para que possa aceder facilmente ao mesmo da próxima vez. Em seguida, pode selecionar **DevTest Labs** no menu de navegação à esquerda.

    ![Todos os serviços - selecione DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. Da lista de laboratórios, selecione o seu laboratório.
1. Na página inicial do laboratório, selecione **Configurações e políticas** no menu esquerdo. 

    :::image type="content" source="./media/activity-logs/configuration-policies-link.png" alt-text="Selecione Configuração e políticas no menu à esquerda":::
1. Na página **de Configuração e políticas,** selecione **'Iniciar sessão de actividade'** no menu esquerdo em **Manage**. Devia ver as entradas para as operações feitas no laboratório. 

    :::image type="content" source="./media/activity-logs/activity-log.png" alt-text="Selecione Configuração e políticas no menu à esquerda":::    
1. Selecione um evento para ver detalhes sobre o mesmo. Na página **Resumo,** vê informações como o nome da operação, o carimbo do tempo e quem fez a operação. 
    
    :::image type="content" source="./media/activity-logs/stop-vm-event.png" alt-text="Selecione Configuração e políticas no menu à esquerda":::        
1. Mude para o separador **JSON** para ver mais detalhes. No exemplo seguinte, pode ver o nome do VM e a operação feita no VM (parado).

    :::image type="content" source="./media/activity-logs/stop-vm-event-json.png" alt-text="Selecione Configuração e políticas no menu à esquerda":::           
1. Mude para o separador Alterar histórico **(Pré-visualização)** para ver o histórico das alterações. No exemplo seguinte, vê-se a alteração que foi feita no VM. 

    :::image type="content" source="./media/activity-logs/change-history.png" alt-text="Selecione Configuração e políticas no menu à esquerda":::             
1. Selecione a alteração na lista de histórico de alteração para ver mais detalhes sobre a mudança. 

    :::image type="content" source="./media/activity-logs/change-details.png" alt-text="Selecione Configuração e políticas no menu à esquerda":::             

Para obter mais informações sobre registos de atividades, consulte [o Registo de Atividades do Azure](../azure-monitor/platform/activity-log.md).

## <a name="next-steps"></a>Passos seguintes

- Para saber sobre a definição **de alertas** nos registos de atividade, consulte [Criar alertas](create-alerts.md).
- Para saber mais sobre os registos de atividades, consulte  [o Registo de Atividades do Azure](../azure-monitor/platform/activity-log.md).

