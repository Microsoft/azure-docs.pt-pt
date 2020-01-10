---
title: Notificações de manutenção para VMs do Azure | Microsoft Docs
description: Visão geral das notificações de manutenção para máquinas virtuais em execução no Azure.
services: virtual-machines
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: f39e5ee7126fe381ad4ba77ec9a876db5f0a1ef6
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75747257"
---
# <a name="handling-planned-maintenance-notifications"></a>Manipulando notificações de manutenção planejada

O Azure realiza periodicamente atualizações para melhorar a fiabilidade, o desempenho e a segurança da infraestrutura de anfitrião para máquinas virtuais. As atualizações são alterações como aplicar patch no ambiente de hospedagem ou atualizar e encerrar o hardware. A maioria dessas atualizações é concluída sem nenhum impacto nas máquinas virtuais hospedadas. No entanto, há casos em que as atualizações têm um impacto:

- Se a manutenção não exigir uma reinicialização, o Azure usará a migração in-loco para pausar a VM enquanto o host for atualizado. Essas operações de manutenção de tipos são aplicadas ao domínio de falha por domínio de falha. O progresso será interrompido se forem recebidos sinais de integridade de aviso.

- Se a manutenção exigir uma reinicialização, você receberá um aviso quando a manutenção for planejada. Você recebe uma janela de tempo de cerca de 35 dias, em que você pode iniciar a manutenção por conta própria, quando ela funciona para você.


A manutenção planejada que exige uma reinicialização é agendada em ondas. Cada onda tem escopo diferente (regiões).

- Uma onda começa com uma notificação para os clientes. Por padrão, a notificação é enviada para o proprietário e os coproprietários da assinatura. Você pode adicionar mais destinatários e opções de mensagens como email, SMS e WebHooks, usando [alertas do log de atividades](../azure-monitor/platform/platform-logs-overview.md).  
- Quando uma notificação sai, uma *janela de autoatendimento* é disponibilizada. Durante essa janela, você pode consultar quais das suas máquinas virtuais são afetadas e iniciar a manutenção com base nas suas necessidades de agendamento. A janela de autoatendimento normalmente é de cerca de 35 dias.
- Após a janela de autoatendimento, uma *janela de manutenção agendada* é iniciada. Em algum momento durante essa janela, o Azure agenda e aplica a manutenção necessária à sua máquina virtual. 

O objetivo de ter duas janelas é fornecer tempo suficiente para iniciar a manutenção e reinicializar sua máquina virtual enquanto souber quando o Azure iniciará automaticamente a manutenção.


Você pode usar o portal do Azure, o PowerShell, a API REST e a CLI para consultar as janelas de manutenção de suas VMs e iniciar a manutenção de autoatendimento.

 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>Você deve iniciar a manutenção usando durante a janela de autoatendimento?  

As diretrizes a seguir devem ajudá-lo a decidir se deve usar essa capacidade e iniciar a manutenção ao seu próprio momento. 

> [!NOTE] 
> A manutenção de autoatendimento pode não estar disponível para todas as suas VMs. Para determinar se a reimplantação proativa está disponível para sua VM, procure o **início agora** no status de manutenção. Atualmente, a manutenção de autoatendimento não está disponível para serviços de nuvem (função Web/de trabalho) e Service Fabric.


A manutenção de autoatendimento não é recomendada para implantações usando **conjuntos de disponibilidade**. Os conjuntos de disponibilidade já estão atualizados apenas em um domínio de atualização por vez. 

- Deixe o Azure disparar a manutenção. Para manutenção que requer reinicialização, a manutenção será feita ao atualizar domínio pelo domínio de atualização. Os domínios de atualização não recebem necessariamente a manutenção sequencialmente e há uma pausa de 30 minutos entre os domínios de atualização. 
- Se uma perda temporária de alguma capacidade (1 domínio de atualização) for uma preocupação, você poderá adicionar instâncias durante o período de manutenção. 
- Para manutenção que não requer reinicialização, as atualizações são aplicadas no nível do domínio de falha. 

**Não** use a manutenção de autoatendimento nos seguintes cenários: 
- Se você desligar suas VMs com frequência, seja manualmente, usando o DevTest Labs, usando o desligamento automático ou seguindo uma agenda, ele poderá reverter o status de manutenção e, portanto, causar tempo de inatividade adicional.
- Em VMs de curta duração, você sabe que será excluído antes do fim da onda de manutenção. 
- Para cargas de trabalho com um estado grande armazenado no disco local (efêmero) que é desejado para ser mantido na atualização. 
- Para casos em que você redimensiona a VM com frequência, pois ela pode reverter o status de manutenção. 
- Se você adotou eventos agendados que habilitam o failover proativo ou o desligamento normal de sua carga de trabalho, 15 minutos antes do início do desligamento da manutenção

**Use** a manutenção de autoatendimento, se você estiver planejando executar sua VM sem interrupção durante a fase de manutenção agendada e nenhuma das indicações de contador mencionadas acima for aplicável. 

É melhor usar a manutenção de autoatendimento nos seguintes casos:
- Você precisa comunicar uma janela de manutenção exata para seu gerenciamento ou cliente final. 
- Você precisa concluir a manutenção por uma determinada data. 
- Você precisa controlar a sequência de manutenção, por exemplo, aplicativo de várias camadas para garantir a recuperação segura.
- Mais de 30 minutos de tempo de recuperação de VM são necessários entre dois domínios de atualização (UDs). Para controlar o tempo entre os domínios de atualização, você deve disparar a manutenção em suas VMs um domínio de atualização (UD) de cada vez.


## <a name="faq"></a>FAQ


**P: por que você precisa reinicializar minhas máquinas virtuais agora?**

**R:** Embora a maioria das atualizações e atualizações na plataforma do Azure não afetem a disponibilidade da máquina virtual, há casos em que não podemos evitar a reinicialização de máquinas virtuais hospedadas no Azure. Acumulamos várias alterações que exigem a reinicialização dos nossos servidores que resultarão na reinicialização das máquinas virtuais.

**P: se eu seguir as recomendações para alta disponibilidade usando um conjunto de disponibilidade, estou seguro?**

**R:** As máquinas virtuais implantadas em um conjunto de disponibilidade ou conjuntos de dimensionamento de máquinas virtuais têm a noção de domínios de atualização (UD). Ao executar a manutenção, o Azure honra a restrição UD e não reinicializará as máquinas virtuais de diferentes UD (dentro do mesmo conjunto de disponibilidade).  O Azure também aguarda pelo menos 30 minutos antes de passar para o próximo grupo de máquinas virtuais. 

Para obter mais informações sobre alta disponibilidade, consulte [disponibilidade para máquinas virtuais no Azure](./linux/availability.md).

**P: Como fazer ser notificado sobre a manutenção planejada?**

**R:** Uma onda de manutenção planejada começa definindo uma agenda para uma ou mais regiões do Azure. Logo após, uma notificação por email é enviada para os proprietários da assinatura (um email por assinatura). Canais e destinatários adicionais para essa notificação podem ser configurados usando alertas do log de atividades. Caso você implante uma máquina virtual em uma região em que a manutenção planejada já esteja agendada, você não receberá a notificação, mas precisará verificar o estado de manutenção da VM.

**P: não vejo nenhuma indicação de manutenção planejada no portal, no PowerShell ou na CLI. Qual é o problema?**

**R:** As informações relacionadas à manutenção planejada estão disponíveis durante uma onda de manutenção planejada somente para as VMs que serão afetadas por ela. Em outras palavras, se você não vir dados, pode ser que a onda de manutenção já tenha sido concluída (ou não iniciada) ou que sua máquina virtual já esteja hospedada em um servidor atualizado.

**P: há uma maneira de saber exatamente quando minha máquina virtual será afetada?**

**R:** Ao definir a agenda, definimos uma janela de tempo de vários dias. No entanto, o sequenciamento exato de servidores (e VMs) nessa janela é desconhecido. Os clientes que gostariam de saber o tempo exato para suas VMs podem usar [eventos agendados](./linux/scheduled-events.md) e consultar de dentro da máquina virtual e receber uma notificação de 15 minutos antes da reinicialização da VM.

**P: quanto tempo levará para reinicializar minha máquina virtual?**

**R:**  Dependendo do tamanho da VM, a reinicialização pode levar até vários minutos durante a janela de manutenção de autoatendimento. Durante as reinicializações iniciadas pelo Azure na janela de manutenção agendada, a reinicialização normalmente levará cerca de 25 minutos. Observe que, caso você use serviços de nuvem (função Web/de trabalho), conjuntos de dimensionamento de máquinas virtuais ou conjuntos de disponibilidade, você receberá 30 minutos entre cada grupo de VMs (UD) durante a janela de manutenção agendada.

**P: Qual é a experiência no caso de conjuntos de dimensionamento de máquinas virtuais?**

**R:** A manutenção planejada agora está disponível para conjuntos de dimensionamento de máquinas virtuais. Para obter instruções sobre como iniciar a manutenção de autoatendimento, consulte documento [manutenção planejada para conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md) .

**P: Qual é a experiência no caso de serviços de nuvem (função Web/de trabalho) e Service Fabric?**

**R:** Embora essas plataformas sejam afetadas pela manutenção planejada, os clientes que usam essas plataformas são considerados seguros, Considerando que somente as VMs em um único domínio de atualização (UD) serão afetadas em um determinado momento. Atualmente, a manutenção de autoatendimento não está disponível para serviços de nuvem (função Web/de trabalho) e Service Fabric.

**P: não vejo nenhuma informação de manutenção em minhas VMs. O que deu errado?**

**R:** Há várias razões pelas quais você não está vendo nenhuma informação de manutenção em suas VMs:
1.  Você está usando uma assinatura marcada como Microsoft Internal.
2.  Suas VMs não estão agendadas para manutenção. Pode ser que a onda de manutenção tenha sido finalizada, cancelada ou modificada para que suas VMs não sejam mais afetadas por ela.
3.  Você não tem a coluna de **manutenção** adicionada ao modo de exibição de lista de VM. Embora tenhamos adicionado essa coluna à exibição padrão, os clientes que configuraram para ver colunas não padrão devem adicionar manualmente a coluna de **manutenção** à sua exibição de lista de VMs.

**P: minha VM está agendada para manutenção pela segunda vez. Por?**

**R:** Há vários casos de uso em que você verá sua VM agendada para manutenção depois que você já tiver concluído sua manutenção-reimplantação:
1.  Cancelamos a onda de manutenção e a reiniciamos com uma carga diferente. Pode ser que detectamos uma carga com falha e simplesmente precisamos implantar uma carga adicional.
2.  Sua *VM foi* reparada para outro nó devido a uma falha de hardware.
3.  Você optou por parar (desalocar) e reiniciar a VM.
4.  Você tem o **desligamento automático** ativado para a VM.



## <a name="next-steps"></a>Passos seguintes

Você pode lidar com a manutenção planejada usando o [CLI do Azure](maintenance-notifications-cli.md), [Azure PowerShell](maintenance-notifications-powershell.md) ou [portal](maintenance-notifications-portal.md).

