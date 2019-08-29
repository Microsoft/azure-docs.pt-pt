---
title: Tratamento de notificações de manutenção para VMs do Linux no Azure | Microsoft Docs
description: Exibir notificações de manutenção para máquinas virtuais Linux em execução no Azure e iniciar a manutenção de autoatendimento.
services: virtual-machines-linux
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 08/22/2019
ms.author: shants
ms.openlocfilehash: 626af4a056274e11ae15670f2f2e2a979eb94fff
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082872"
---
# <a name="handling-planned-maintenance-notifications-for-linux-virtual-machines"></a>Lidando com notificações de manutenção planejada para máquinas virtuais do Linux

O Azure realiza periodicamente atualizações para melhorar a fiabilidade, o desempenho e a segurança da infraestrutura de anfitrião para máquinas virtuais. As atualizações são alterações como aplicar patch no ambiente de hospedagem ou atualizar e encerrar o hardware. A maioria dessas atualizações é executada sem nenhum impacto nas máquinas virtuais hospedadas. No entanto, há casos em que as atualizações têm um impacto:

- Se a manutenção não exigir uma reinicialização, o Azure usará a migração in-loco para pausar a VM enquanto o host for atualizado. Essas operações de manutenção não reinicializadas são aplicadas ao domínio de falha por domínio de falha e o progresso é interrompido se forem recebidos sinais de integridade de aviso.

- Se a manutenção exigir uma reinicialização, você receberá um aviso quando a manutenção for planejada. Nesses casos, você recebe uma janela de tempo que normalmente é de 35 dias em que você pode iniciar a manutenção por conta própria, quando ela funciona para você.


A manutenção planejada que exige uma reinicialização é agendada em ondas. Cada onda tem escopo diferente (regiões).

- Uma onda começa com uma notificação para os clientes. Por padrão, a notificação é enviada para o proprietário e os coproprietários da assinatura. Você pode adicionar mais destinatários e opções de mensagens, como email, SMS e WebHooks, às notificações usando [alertas do log de atividades](../../azure-monitor/platform/activity-logs-overview.md)do Azure.  
- No momento da notificação, uma janela de *autoatendimento* é disponibilizada. Durante essa janela que normalmente é de 35 dias, você pode encontrar quais máquinas virtuais estão incluídas nessa onda e iniciar de forma proativa a manutenção de acordo com suas necessidades de agendamento.
- Após a janela de autoatendimento, uma *janela de manutenção agendada* é iniciada. Em algum momento durante essa janela, o Azure agenda e aplica a manutenção necessária à sua máquina virtual. 

O objetivo de ter duas janelas é fornecer tempo suficiente para iniciar a manutenção e reinicializar sua máquina virtual enquanto souber quando o Azure iniciará automaticamente a manutenção.


Você pode usar o portal do Azure, o PowerShell, a API REST e a CLI para consultar as janelas de manutenção de suas VMs e iniciar a manutenção de autoatendimento.

 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>Você deve iniciar a manutenção usando durante a janela de autoatendimento?  

As diretrizes a seguir devem ajudá-lo a decidir se deve usar essa capacidade e iniciar a manutenção ao seu próprio momento. 

> [!NOTE] 
> A manutenção de autoatendimento pode não estar disponível para todas as suas VMs. Para determinar se a reimplantação proativa está disponível para sua VM, procure o **início agora** no status de manutenção. Atualmente, a manutenção de autoatendimento não está disponível para serviços de nuvem (função Web/de trabalho) e Service Fabric.


A manutenção de autoatendimento não é recomendada para implantações usando **conjuntos de disponibilidade** , pois essas são configurações altamente disponíveis, em que apenas um domínio de atualização é afetado em um determinado momento. 
- Deixe o Azure disparar a manutenção. Para manutenção que requer reinicialização, lembre-se de que a manutenção será feita ao atualizar o domínio pelo domínio de atualização, que os domínios de atualização não receberão necessariamente a manutenção sequencialmente e que haja uma pausa de 30 minutos entre os domínios de atualização. 
- Se uma perda temporária de alguma capacidade (1/contagem de domínio de atualização) for uma preocupação, ela poderá ser facilmente compensada alocando instâncias de adição durante o período de manutenção. 
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
- Você precisa de mais de 30 minutos de tempo de recuperação de VM entre dois domínios de atualização (UDs). Para controlar o tempo entre os domínios de atualização, você deve disparar a manutenção em suas VMs um domínio de atualização (UD) de cada vez.



## <a name="find-vms-scheduled-for-maintenance-using-cli"></a>Localizar VMs agendadas para manutenção usando a CLI

As informações de manutenção planejada podem ser vistas usando o [Azure VM Get-Instance-View](/cli/azure/vm?view=azure-cli-latest).
 
As informações de manutenção serão retornadas somente se houver manutenção planejada. Se não houver nenhuma manutenção agendada que afete a VM, o comando não retornará nenhuma informação de manutenção. 

```azure-cli
az vm get-instance-view -g rgName -n vmName
```

Os valores a seguir são retornados em MaintenanceRedeployStatus: 

| Value | Descrição   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se você pode iniciar a manutenção na VM no momento |
| PreMaintenanceWindowStartTime         | O início da janela de autoatendimento de manutenção quando você pode iniciar a manutenção em sua VM |
| PreMaintenanceWindowEndTime           | O final da janela de autoatendimento de manutenção quando você pode iniciar a manutenção em sua VM |
| MaintenanceWindowStartTime            | O início da janela de manutenção agendada na qual o Azure inicia a manutenção em sua VM |
| MaintenanceWindowEndTime              | O fim da janela de manutenção agendada na qual o Azure inicia a manutenção em sua VM |
| LastOperationResultCode               | O resultado da última tentativa de iniciar a manutenção na VM |




## <a name="start-maintenance-on-your-vm-using-cli"></a>Iniciar a manutenção em sua VM usando a CLI

A chamada a seguir iniciará a manutenção em uma `IsCustomerInitiatedMaintenanceAllowed` VM se for definido como true.

```azure-cli
az vm perform-maintenance -g rgName -n vmName 
```

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="classic-deployments"></a>Implementações clássicas

Se você ainda tiver VMs herdadas que foram implantadas usando o modelo de implantação clássico, poderá usar a CLI clássica do Azure para consultar as VMs e iniciar a manutenção.

Verifique se você está no modo correto para trabalhar com a VM clássica digitando:

```
azure config mode asm
```

Para obter o status de manutenção de uma VM denominada *myVM*, digite:

```
azure vm show myVM 
``` 

Para iniciar a manutenção em sua VM clássica denominada *myVM* no serviço MyService e na implantação de mydeployment, digite:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```


## <a name="faq"></a>FAQ


**P: Por que você precisa reinicializar minhas máquinas virtuais agora?**

**R:** Embora a maioria das atualizações e atualizações na plataforma do Azure não afetem a disponibilidade da máquina virtual, há casos em que não podemos evitar a reinicialização de máquinas virtuais hospedadas no Azure. Acumulamos várias alterações que exigem a reinicialização dos nossos servidores que resultarão na reinicialização das máquinas virtuais.

**P: Se eu seguir suas recomendações para alta disponibilidade usando um conjunto de disponibilidade, estou seguro?**

**R:** As máquinas virtuais implementadas num conjunto de disponibilidade ou em conjuntos de dimensionamento de máquinas virtuais têm a noção de Domínios de Atualização (UD). Ao executar a manutenção, o Azure honra a restrição UD e não reinicializará as máquinas virtuais de diferentes UD (dentro do mesmo conjunto de disponibilidade).  O Azure também aguarda pelo menos 30 minutos antes de passar para o próximo grupo de máquinas virtuais. 

Para obter mais informações sobre alta disponibilidade, consulte [disponibilidade para máquinas virtuais no Azure](availability.MD).

**P: Como fazer ser notificado sobre a manutenção planejada?**

**R:** Uma onda de manutenção planejada começa definindo uma agenda para uma ou mais regiões do Azure. Logo após, uma notificação por email é enviada para os proprietários da assinatura (um email por assinatura). Canais e destinatários adicionais para essa notificação podem ser configurados usando alertas do log de atividades. Caso você implante uma máquina virtual em uma região em que a manutenção planejada já esteja agendada, você não receberá a notificação, mas precisará verificar o estado de manutenção da VM.

**P: Não vejo nenhuma indicação de manutenção planejada no portal, no PowerShell ou na CLI. Qual é o problema?**

**R:** As informações relacionadas à manutenção planejada estão disponíveis durante uma onda de manutenção planejada somente para as VMs que serão afetadas por ela. Em outras palavras, se você não vir dados, pode ser que a onda de manutenção já tenha sido concluída (ou não iniciada) ou que sua máquina virtual já esteja hospedada em um servidor atualizado.

**P: Há uma maneira de saber exatamente quando minha máquina virtual será afetada?**

**R:** Ao definir a agenda, definimos uma janela de tempo de vários dias. No entanto, o sequenciamento exato de servidores (e VMs) nessa janela é desconhecido. Os clientes que gostariam de saber o tempo exato para suas VMs podem usar [eventos agendados](scheduled-events.md) e consultar de dentro da máquina virtual e receber uma notificação de 15 minutos antes da reinicialização da VM.

**P: Quanto tempo levará para reinicializar minha máquina virtual?**

**R:**  Dependendo do tamanho da VM, a reinicialização pode levar até vários minutos durante a janela de manutenção de autoatendimento. Durante as reinicializações iniciadas pelo Azure na janela de manutenção agendada, a reinicialização normalmente levará cerca de 25 minutos. Observe que, caso você use serviços de nuvem (função Web/de trabalho), conjuntos de dimensionamento de máquinas virtuais ou conjuntos de disponibilidade, você receberá 30 minutos entre cada grupo de VMs (UD) durante a janela de manutenção agendada.

**P: Qual é a experiência no caso de conjuntos de dimensionamento de máquinas virtuais?**

**R:** A manutenção planejada agora está disponível para conjuntos de dimensionamento de máquinas virtuais. Para obter instruções sobre como iniciar a manutenção de autoatendimento, consulte documento [manutenção planejada para conjuntos de dimensionamento de máquinas virtuais](../../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md) .

**P: Qual é a experiência no caso de serviços de nuvem (função Web/de trabalho) e Service Fabric?**

**R:** Apesar de estas plataformas serem afetadas pela manutenção planeada, os clientes que utilizam estas plataformas são considerados seguros, uma vez que apenas as VMs num Domínio de Atualização (UD) único serão impactadas em qualquer momento. Atualmente, a manutenção de autoatendimento não está disponível para serviços de nuvem (função Web/de trabalho) e Service Fabric.

**P: Não vejo nenhuma informação de manutenção em minhas VMs. O que deu errado?**

**R:** Há várias razões pelas quais você não está vendo nenhuma informação de manutenção em suas VMs:
1.  Você está usando uma assinatura marcada como Microsoft Internal.
2.  Suas VMs não estão agendadas para manutenção. Pode ser que a onda de manutenção tenha sido finalizada, cancelada ou modificada para que suas VMs não sejam mais afetadas por ela.
3.  Você não tem a coluna de **manutenção** adicionada ao modo de exibição de lista de VM. Embora tenhamos adicionado essa coluna à exibição padrão, os clientes que configuraram para ver colunas não padrão devem adicionar manualmente a coluna de **manutenção** à sua exibição de lista de VMs.

**P: Minha VM está agendada para manutenção pela segunda vez. Porquê?**

**R:** Existem vários casos de utilização em que verá a sua VM agendada para manutenção depois de já ter concluído a reimplementação da manutenção:
1.  Cancelamos a onda de manutenção e a reiniciamos com uma carga diferente. Pode ser que detectamos uma carga com falha e simplesmente precisamos implantar uma carga adicional.
2.  Sua VM foi reparada para outro nó devido a uma falha de hardware.
3.  Você optou por parar (desalocar) e reiniciar a VM.
4.  Você tem o desligamento **automático** ativado para a VM.


## <a name="next-steps"></a>Passos Seguintes

Saiba como você pode se registrar para eventos de manutenção de dentro da VM usando [eventos agendados](scheduled-events.md).
