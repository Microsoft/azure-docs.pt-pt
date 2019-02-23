---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 07/02/2018
ms.author: shants
ms.custom: include file
ms.openlocfilehash: 50a215175d7305834a64b7e0cfbc153431b10b7c
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56739206"
---
## <a name="view-vms-scheduled-for-maintenance-in-the-portal"></a>View VMS scheduled para manutenção no portal de VMs

Assim que estiver agendada uma onda de manutenção planeada, pode observar que a lista de máquinas virtuais que são afetadas pela onda de manutenção futura. 

Pode utilizar o portal do Azure e procurar por VMs agendadas para manutenção.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No painel de navegação esquerdo, clique em **máquinas virtuais**.

3. No painel de máquinas virtuais, clique nas **colunas** botão para abrir a lista de colunas disponíveis.

4. Selecione e adicione as seguintes colunas:

   **Manutenção**: Mostra o estado de manutenção para a VM. Seguem-se os valores possíveis:
      
      | Value | Descrição |
      |-------|-------------|
      | Começar agora | A VM está na janela de manutenção self-service que lhe permite iniciar a manutenção por conta própria. Veja abaixo saber como iniciar a manutenção na sua VM. | 
      | Agendadas | A VM está agendada para manutenção sem qualquer opção para iniciar a manutenção. Pode aprender da janela de manutenção selecionando a manutenção - janela agendada nesta vista ou ao clicar na VM. | 
      | Já está atualizado | A VM já está atualizada e é necessária nenhuma ação adicional neste momento. | 
      | Tente novamente mais tarde | Iniciou a manutenção sem sucesso. Será capaz de usar a opção de manutenção self-service num momento posterior. | 
      | Repetir agora | Pode tentar novamente uma manutenção Self-iniciada anteriormente sem êxito. | 
      | - | A VM não faz parte de uma onda de manutenção planeada. |
      

   **Manutenção - janela de self-service**: Mostra a janela de tempo quando Self-pode iniciar a manutenção nas suas VMs.
   
   **Manutenção - janela agendada**: Mostra a janela de tempo quando o Azure manterá sua VM para concluir a manutenção. 



## <a name="notification-and-alerts-in-the-portal"></a>Notificações e alertas no portal

Azure comunica um agendamento para a manutenção planeada ao enviar um e-mail para o grupo proprietário e os coproprietários de subscrição. Pode adicionar os destinatários adicionais e canais para esta comunicação através da criação de alertas de registo de atividades do Azure. Para obter mais informações, consulte [criar alertas do registo de atividade nas notificações do serviço](../articles/azure-monitor/platform/alerts-activity-log-service-notifications.md).

Certifique-se de definir o **tipo de evento** como **manutenção planeada** e **serviços** como **conjuntos de dimensionamento de máquinas virtuais** e/ou **Máquinas virtuais**
    
    
## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Iniciar a manutenção em sua VM a partir do portal

Ao olhar para os detalhes da VM, poderá ver mais detalhes relacionados com a manutenção.  
Na parte superior da vista de detalhes de VM, será adicionada uma nova faixa de opções de notificação se a sua VM está incluída numa onda de manutenção planeada. Além disso, é adicionada uma nova opção para iniciar a manutenção sempre que possível. 


Clique na notificação de manutenção para ver a página de manutenção com mais detalhes sobre a manutenção planeada. A partir daí, vai conseguir **iniciar manutenção** na sua VM.

Assim que iniciar a manutenção, sua máquina virtual será mantida, e o estado de manutenção será atualizado para refletir o resultado dentro de alguns minutos.

Se perdeu a janela de self-service, ainda será capaz de ver a janela quando a VM será mantida pelo Azure. 
