---
title: Utilize o portal para notificações de manutenção
description: Veja as notificações de manutenção para máquinas virtuais em funcionamento em Azure e inicie a manutenção do self-service, utilizando o portal.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 46fcc825ac49f0181ac74e9c3e2deaea577f3329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77115735"
---
# <a name="handling-planned-maintenance-notifications-using-the-portal"></a>Manuseamento de notificações de manutenção planeadas utilizando o portal

**Este artigo aplica-se a máquinas virtuais que executam tanto o Linux como o Windows.**

Uma vez programada uma onda de [manutenção planeada,](maintenance-notifications.md) pode verificar se há uma lista de máquinas virtuais que são impactadas. 

Pode utilizar o portal Azure e procurar VMs programados para manutenção.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Na navegação à esquerda, clique em **Máquinas Virtuais**.

3. No painel máquinas virtuais, selecione o botão **de colunas Editar** para abrir a lista das colunas disponíveis.

4. Selecione e adicione as seguintes colunas:

   **Estado de manutenção**: Mostra o estado de manutenção do VM. Seguem-se os valores potenciais:
      
      | Valor | Descrição |
      |-------|-------------|
      | Iniciar agora | O VM está na janela de manutenção de self-service que permite iniciar a manutenção por si mesmo. Veja abaixo como iniciar a manutenção no seu VM. | 
      | Agendado | A VM está agendada para manutenção sem qualquer opção para iniciar a manutenção. Pode aprender a janela de manutenção selecionando a janela de manutenção - Programada nesta vista ou clicando no VM. | 
      | Já atualizado | O seu VM já está atualizado e não são necessárias mais medidas neste momento. | 
      | Voltar a tentar mais tarde | Iniciou a manutenção sem sucesso. Poderá utilizar a opção de manutenção de self-service mais tarde. | 
      | Voltar a tentar agora | Pode voltar a tentar uma manutenção auto-iniciada anteriormente fracassada. | 
      | - | O seu VM não faz parte de uma onda de manutenção planeada. |
      

   **Manutenção - Janela de self-service**: Mostra a janela de tempo quando pode iniciar a manutenção dos seus VMs.
   
   **Manutenção - Janela programada**: Mostra a janela de tempo quando o Azure manterá o seu VM para completar a manutenção. 



## <a name="notification-and-alerts-in-the-portal"></a>Notificação e alertas no portal

O Azure comunica um calendário para a manutenção planeada enviando um e-mail ao proprietário da subscrição e ao grupo de coproprietários. Pode adicionar destinatários e canais adicionais a esta comunicação criando alertas de registo de atividade do Azure. Para mais informações, consulte Criar alertas de registo de [atividade sonantes em notificações](../azure-monitor/platform/alerts-activity-log-service-notifications.md)de serviço .

Certifique-se de que define o **tipo de Evento** como manutenção **planeada,** e **serviços** como **conjuntos** de escala de máquina virtual e/ou **máquinas virtuais**.

## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Inicie a Manutenção no seu VM a partir do portal

Ao ver os detalhes do VM, poderá ver mais detalhes relacionados com a manutenção.  
No topo da vista de detalhes vm, uma nova fita de notificação será adicionada se o seu VM estiver incluído numa onda de manutenção planeada. Além disso, é adicionada uma nova opção para iniciar a manutenção quando possível. 


Clique na notificação de manutenção para ver a página de manutenção com mais detalhes sobre a manutenção planeada. A partir daí, poderá iniciar a **manutenção** do seu VM.

Assim que iniciar a manutenção, a sua máquina virtual será mantida e o estado de manutenção será atualizado para refletir o resultado dentro de poucos minutos.

Se perdeu a janela de self-service, ainda poderá ver a janela quando o seu VM será mantido pelo Azure. 


## <a name="next-steps"></a>Passos seguintes

Também pode manusear a manutenção planeada utilizando o [Azure CLI](maintenance-notifications-cli.md) ou [powerShell](maintenance-notifications-powershell.md).