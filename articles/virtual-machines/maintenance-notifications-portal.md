---
title: Utilize o portal para notificações de manutenção
description: Ver notificações de manutenção para máquinas virtuais em funcionamento em Azure e iniciar a manutenção de self-service, utilizando o portal.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: a13d79a28ac07d736b1eaf0d0e6b7f7b1ba4a4ec
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557714"
---
# <a name="handling-planned-maintenance-notifications-using-the-portal"></a>Manipulação de notificações de manutenção planeadas utilizando o portal

**Este artigo aplica-se a máquinas virtuais que executam o Linux e o Windows.**

Uma vez agendada uma onda [de manutenção planeada,](maintenance-notifications.md) pode verificar se existe uma lista de máquinas virtuais que são impactadas. 

Pode utilizar o portal Azure e procurar VMs agendados para manutenção.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Na navegação à esquerda, clique em **Máquinas Virtuais.**

3. No painel de máquinas virtuais, **selecione editar** o botão colunas para abrir a lista de colunas disponíveis.

4. Selecione e adicione as seguintes colunas:

   **Estado de manutenção**: Mostra o estado de manutenção do VM. Seguem-se os valores potenciais:
      
    | Valor | Descrição |
    |-------|-------------|
    | Iniciar agora | O VM encontra-se na janela de manutenção de autosserviço que lhe permite iniciar a manutenção. Veja abaixo como iniciar a manutenção no seu VM. | 
    | Agendado | O VM está agendado para manutenção sem opção de iniciar a manutenção. Pode conhecer a janela de manutenção selecionando a janela Manutenção - Janela programada nesta vista ou clicando no VM. | 
    | Já atualizado | O seu VM já está atualizado e não são necessárias mais medidas neste momento. | 
    | Redaçar mais tarde | Iniciou a manutenção sem sucesso. Poderá utilizar a opção de manutenção de autosserviço mais tarde. | 
    | Redaçar agora | Pode voltar a tentar uma manutenção auto-iniciada sem sucesso. | 
    | - | O seu VM não faz parte de uma onda de manutenção planeada. |

   **Manutenção - Janela de autosserviço**: Mostra a janela de tempo quando pode iniciar a manutenção auto-iniciada nos seus VMs.
   
   **Manutenção - Janela programada**: Mostra a janela de tempo em que o Azure manterá o seu VM para completar a manutenção. 



## <a name="notification-and-alerts-in-the-portal"></a>Notificação e alertas no portal

O Azure comunica um horário de manutenção planeada enviando um e-mail ao proprietário da assinatura e ao grupo de coproprietários. Pode adicionar destinatários e canais adicionais a esta comunicação criando alertas de registo de atividades Azure. Para obter mais informações, consulte [Criar alertas de registo de atividades nas notificações de serviço](../service-health/alerts-activity-log-service-notifications-portal.md).

Certifique-se de que define o **tipo de Evento** como manutenção **planeada**, e **serviços** como **conjuntos de balança de máquinas virtuais** e/ou **máquinas virtuais.**

## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Inicie a Manutenção no seu VM a partir do portal

Ao analisar os detalhes do VM, poderá ver mais detalhes relacionados com a manutenção.  
No topo da vista de detalhes VM, uma nova fita de notificação será adicionada se o seu VM estiver incluído numa onda de manutenção planeada. Além disso, é adicionada uma nova opção para iniciar a manutenção quando possível. 


Clique na notificação de manutenção para ver a página de manutenção com mais detalhes sobre a manutenção planeada. A partir daí, poderá iniciar a **manutenção** do seu VM.

Assim que iniciar a manutenção, a sua máquina virtual será mantida e o estado de manutenção será atualizado para refletir o resultado dentro de poucos minutos.

Se perdeu a janela de autosserviço, ainda poderá ver a janela quando o seu VM será mantido pelo Azure. 


## <a name="next-steps"></a>Passos seguintes

Também pode manusear a manutenção planeada utilizando o [Azure CLI](maintenance-notifications-cli.md) ou [o PowerShell](maintenance-notifications-powershell.md).
