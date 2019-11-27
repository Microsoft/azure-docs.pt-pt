---
title: Usar o portal para notificações de manutenção para VMs do Azure
description: Exibir notificações de manutenção para máquinas virtuais em execução no Azure e iniciar a manutenção de autoatendimento, usando o Portal.
services: virtual-machines
author: shants123
tags: azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 759fbc5ba3c5eaa78fec1045bcf41969108d39b1
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535824"
---
# <a name="handling-planned-maintenance-notifications-using-the-portal"></a>Lidando com notificações de manutenção planejada usando o portal

**Este artigo se aplica a máquinas virtuais que executam o Linux e o Windows.**

Depois que uma onda de [manutenção planejada](maintenance-notifications.md) for agendada, você poderá verificar se há uma lista de máquinas virtuais afetadas. 

Você pode usar o portal do Azure e procurar VMs agendadas para manutenção.

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).

2. No painel de navegação esquerdo, clique em **máquinas virtuais**.

3. No painel máquinas virtuais, selecione o botão **Editar colunas** para abrir a lista de colunas disponíveis.

4. Selecione e adicione as seguintes colunas:

   **Status de manutenção**: mostra o status de manutenção para a VM. Estes são os valores possíveis:
      
      | Valor | Descrição |
      |-------|-------------|
      | Começar agora | A VM está na janela de manutenção de autoatendimento que permite que você inicie a manutenção por conta própria. Veja abaixo como iniciar a manutenção em sua VM. | 
      | Agendado | A VM está agendada para manutenção sem qualquer opção para iniciar a manutenção. Você pode aprender sobre a janela de manutenção selecionando a janela de manutenção agendada neste modo de exibição ou clicando na VM. | 
      | Já atualizado | Sua VM já está atualizada e nenhuma ação adicional é necessária no momento. | 
      | Tentar novamente mais tarde | Você iniciou a manutenção sem êxito. Você poderá usar a opção de manutenção de autoatendimento em um momento posterior. | 
      | Tentar novamente agora | Você pode tentar novamente uma manutenção autoiniciada anteriormente malsucedida. | 
      | - | Sua VM não faz parte de uma onda de manutenção planejada. |
      

   **Manutenção – janela de autoatendimento**: mostra a janela de tempo quando você pode iniciar automaticamente a manutenção em suas VMs.
   
   **Manutenção-janela agendada**: mostra a janela de tempo quando o Azure manterá sua VM para concluir a manutenção. 



## <a name="notification-and-alerts-in-the-portal"></a>Notificação e alertas no portal

O Azure comunica uma agenda para manutenção planejada enviando um email para o grupo de proprietários e coproprietários da assinatura. Você pode adicionar mais destinatários e canais a essa comunicação criando alertas do log de atividades do Azure. Para obter mais informações, consulte [criar alertas do log de atividades em notificações de serviço](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

Certifique-se de definir o **tipo de evento** como **manutenção planejada**e **Serviços** como conjuntos de **dimensionamento** de máquinas virtuais e/ou **máquinas virtuais**.

## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Iniciar a manutenção em sua VM no portal

Ao examinar os detalhes da VM, você poderá ver mais detalhes relacionados à manutenção.  
Na parte superior da exibição detalhes da VM, uma nova faixa de uma notificação será adicionada se a VM estiver incluída em uma onda de manutenção planejada. Além disso, uma nova opção é adicionada para iniciar a manutenção quando possível. 


Clique na notificação de manutenção para ver a página manutenção com mais detalhes sobre a manutenção planejada. A partir daí, você poderá iniciar a **manutenção** em sua VM.

Depois de iniciar a manutenção, sua máquina virtual será mantida e o status de manutenção será atualizado para refletir o resultado em alguns minutos.

Se você perdeu a janela de autoatendimento, ainda poderá ver a janela quando sua VM será mantida pelo Azure. 


## <a name="next-steps"></a>Passos Seguintes

Você também pode manipular a manutenção planejada usando o [CLI do Azure](maintenance-notifications-cli.md) ou o [PowerShell](maintenance-notifications-powershell.md).