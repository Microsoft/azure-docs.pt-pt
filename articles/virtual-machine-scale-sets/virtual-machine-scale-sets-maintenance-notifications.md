---
title: Notificações de manutenção para conjuntos de dimensionamento de máquinas virtuais no Azure
description: Exibir notificações de manutenção e iniciar a manutenção de autoatendimento para conjuntos de dimensionamento de máquinas virtuais no Azure.
author: shants123
tags: azure-service-management,azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: shants
ms.openlocfilehash: 8d8c32c2a2f3e31c1b7f4645fe61abf2d5d0e014
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275786"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Notificações de manutenção planejada para conjuntos de dimensionamento de máquinas virtuais


Periodicamente, o Azure realiza atualizações para melhorar a confiabilidade, o desempenho e a segurança da infraestrutura de host para VMs (máquinas virtuais). As atualizações podem incluir a aplicação de patch no ambiente de hospedagem ou a atualização e o encerramento do hardware. A maioria das atualizações não afeta as VMs hospedadas. No entanto, as atualizações afetam as VMs nesses cenários:

- Se a manutenção não exigir uma reinicialização, o Azure usará a migração in-loco para pausar a VM enquanto o host for atualizado. As operações de manutenção que não exigem uma reinicialização são aplicadas ao domínio de falha por domínio de falha. O progresso será interrompido se forem recebidos sinais de integridade de aviso.

- Se a manutenção exigir uma reinicialização, você receberá um aviso quando a manutenção for planejada. Nesses casos, você recebe uma janela de tempo que normalmente é de 35 dias em que você pode iniciar a manutenção por conta própria, quando ela funciona para você.


A manutenção planejada que exige uma reinicialização é agendada em ondas. Cada onda tem escopo diferente (regiões):

- Uma onda começa com uma notificação para os clientes. Por padrão, a notificação é enviada para o proprietário e os coproprietários da assinatura. Você pode adicionar destinatários e opções de mensagens como email, SMS e WebHooks às notificações usando [alertas do log de atividades](../azure-monitor/platform/platform-logs-overview.md)do Azure.  
- Com a notificação, uma *janela de autoatendimento* é disponibilizada. Durante essa janela que normalmente é de 35 dias, você pode encontrar quais das suas VMs estão incluídas na onda. Você pode iniciar a manutenção proativamente de acordo com suas necessidades de agendamento.
- Após a janela de autoatendimento, uma *janela de manutenção agendada* é iniciada. Em algum momento durante essa janela, o Azure agenda e aplica a manutenção necessária à sua VM. 

O objetivo de ter duas janelas é fornecer tempo suficiente para iniciar a manutenção e reinicializar sua VM, sabendo quando o Azure iniciará automaticamente a manutenção.

Você pode usar o portal do Azure, o PowerShell, a API REST e o CLI do Azure para consultar as janelas de manutenção de suas VMs do conjunto de dimensionamento de máquinas virtuais e iniciar a manutenção de autoatendimento.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Você deve iniciar a manutenção durante a janela de autoatendimento?  

As diretrizes a seguir podem ajudá-lo a decidir se deseja iniciar a manutenção de cada vez que escolher.

> [!NOTE] 
> A manutenção de autoatendimento pode não estar disponível para todas as suas VMs. Para determinar se a reimplantação proativa está disponível para sua VM, procure **Iniciar agora** no status de manutenção. Atualmente, a manutenção de autoatendimento não está disponível para os serviços de nuvem do Azure (função Web/de trabalho) e Service Fabric do Azure.


A manutenção de autoatendimento não é recomendada para implantações que usam *conjuntos de disponibilidade*. Os conjuntos de disponibilidade são configurações altamente disponíveis nas quais apenas um domínio de atualização é afetado a qualquer momento. Para conjuntos de disponibilidade:

- Deixe o Azure disparar a manutenção. Para manutenção que requer uma reinicialização, a manutenção é concluída domínio de atualização por domínio de atualização. Os domínios de atualização não recebem necessariamente a manutenção sequencialmente. Há uma pausa de 30 minutos entre os domínios de atualização.
- Se uma perda temporária de alguma capacidade (1/contagem de domínio de atualização) for uma preocupação, você poderá compensar facilmente a perda alocando instâncias adicionais durante o período de manutenção.
- Para a manutenção que não exige uma reinicialização, as atualizações são aplicadas no nível do domínio de falha. 
    
**Não** use a manutenção de autoatendimento nos seguintes cenários: 

- Se você desligar suas VMs com frequência, seja manualmente, usando o DevTest Labs, usando o desligamento automático ou seguindo uma agenda. A manutenção de autoatendimento nesses cenários pode reverter o status de manutenção e causar tempo de inatividade adicional.
- Em VMs de curta duração, você sabe que será excluído antes do fim da onda de manutenção. 
- Para cargas de trabalho com um estado grande armazenado no disco local (efêmero) que você deseja manter após a atualização. 
- Se você redimensionar sua VM com frequência. Esse cenário pode reverter o status de manutenção. 
- Se você adotou eventos agendados que habilitam o failover proativo ou o desligamento normal da carga de trabalho 15 minutos antes do desligamento da manutenção, comece.

**Use a** manutenção de autoatendimento se você planeja executar a VM sem interrupção durante a fase de manutenção agendada e nenhuma das contraindicações anteriores se aplicar. 

É melhor usar a manutenção de autoatendimento nos seguintes casos:

- Você precisa comunicar uma janela de manutenção exata para o gerenciamento ou o cliente. 
- Você precisa concluir a manutenção por uma data específica. 
- Você precisa controlar a sequência de manutenção, por exemplo, em um aplicativo de várias camadas, para garantir a recuperação segura.
- Você precisa de mais de 30 minutos de tempo de recuperação de VM entre dois domínios de atualização. Para controlar o tempo entre os domínios de atualização, você deve disparar a manutenção em suas VMs um domínio de atualização por vez.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>Exibir conjuntos de dimensionamento de máquinas virtuais que são afetados pela manutenção no portal

Quando uma onda de manutenção planejada é agendada, você pode exibir a lista de conjuntos de dimensionamento de máquinas virtuais que são afetados pela próxima onda de manutenção usando o portal do Azure. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No menu à esquerda, selecione **todos os serviços**e, em seguida, selecione **conjuntos de dimensionamento de máquinas virtuais**.
3. Em **conjuntos de dimensionamento de máquinas virtuais**, selecione **Editar colunas** para abrir a lista de colunas disponíveis.
4. Na seção **colunas disponíveis** , selecione **manutenção de autoatendimento**e, em seguida, mova-o para a lista **colunas selecionadas** . Selecione **Aplicar**.  

    Para facilitar a localização do item de **manutenção de autoatendimento** , você pode alterar a opção suspensa na seção **colunas disponíveis** de **todos** para **Propriedades**.

A coluna **manutenção de autoatendimento** agora aparece na lista de conjuntos de dimensionamento de máquinas virtuais. Cada conjunto de dimensionamento de máquinas virtuais pode ter um dos seguintes valores para a coluna de manutenção de autoatendimento:

| Valor | Descrição |
|-------|-------------|
| Sim | Pelo menos uma VM no conjunto de dimensionamento de máquinas virtuais está em uma janela de autoatendimento. Você pode iniciar a manutenção a qualquer momento durante este período de autoatendimento. | 
| Não | Nenhuma VM está em um período de autoatendimento no conjunto de dimensionamento de máquinas virtuais afetado. | 
| - | Os conjuntos de dimensionamento de máquinas virtuais não fazem parte de uma onda de manutenção planejada.| 

## <a name="notification-and-alerts-in-the-portal"></a>Notificação e alertas no portal

O Azure comunica uma agenda para manutenção planejada enviando um email para o grupo de proprietários e coproprietários da assinatura. Você pode adicionar destinatários e canais a essa comunicação criando alertas do log de atividades. Para obter mais informações, consulte [monitorar a atividade de assinatura com o log de atividades do Azure](../azure-monitor/platform/platform-logs-overview.md).

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No menu à esquerda, selecione **Monitor**. 
3. No painel **monitorar alertas (clássico)** , selecione **+ adicionar alerta do log de atividades**.
4. Na página **adicionar alerta do log de atividades** , selecione ou insira as informações solicitadas. Em **critérios**, certifique-se de definir os seguintes valores:
   - **Categoria de evento**: selecione **integridade do serviço**.
   - **Serviços**: selecione **conjuntos de dimensionamento de máquinas virtuais e máquinas virtuais**.
   - **Tipo**: selecione **manutenção planejada**. 
    
Para saber mais sobre como configurar alertas do log de atividades, confira [criar alertas do log de atividades](../azure-monitor/platform/activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Iniciar a manutenção no conjunto de dimensionamento de máquinas virtuais do portal

Você pode ver mais detalhes relacionados à manutenção na visão geral dos conjuntos de dimensionamento de máquinas virtuais. Se pelo menos uma VM no conjunto de dimensionamento de máquinas virtuais estiver incluída na onda de manutenção planejada, uma nova faixa de uma notificação será adicionada próximo à parte superior da página. Selecione a faixa de opções de notificação para ir para a página **manutenção** . 

Na página **manutenção** , você pode ver qual instância de VM é afetada pela manutenção planejada. Para iniciar a manutenção, marque a caixa de seleção que corresponde à VM afetada. Em seguida, selecione **Iniciar manutenção**.

Depois de iniciar a manutenção, as VMs afetadas no conjunto de dimensionamento de máquinas virtuais passam por manutenção e ficam temporariamente indisponíveis. Se você perdeu a janela de autoatendimento, ainda poderá ver a janela de tempo quando o conjunto de dimensionamento de máquinas virtuais for mantido pelo Azure.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Verificar o status de manutenção usando o PowerShell

Você pode usar Azure PowerShell para ver quando as VMs em seus conjuntos de dimensionamento de máquinas virtuais estão agendadas para manutenção. As informações de manutenção planejada estão disponíveis usando o cmdlet [Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss) quando você usa o parâmetro `-InstanceView`.
 
As informações de manutenção serão retornadas somente se a manutenção for planejada. Se nenhuma manutenção estiver agendada que afete a instância de VM, o cmdlet não retornará nenhuma informação de manutenção. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

As propriedades a seguir são retornadas em **MaintenanceRedeployStatus**: 

| Valor | Descrição   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se você pode iniciar a manutenção na VM no momento. |
| PreMaintenanceWindowStartTime         | O início da janela de autoatendimento de manutenção quando você pode iniciar a manutenção em sua VM. |
| PreMaintenanceWindowEndTime           | O final da janela de autoatendimento de manutenção quando você pode iniciar a manutenção em sua VM. |
| MaintenanceWindowStartTime            | O início da manutenção agendada na qual o Azure inicia a manutenção em sua VM. |
| MaintenanceWindowEndTime              | O fim da janela de manutenção agendada na qual o Azure inicia a manutenção em sua VM. |
| LastOperationResultCode               | O resultado da última tentativa de iniciar a manutenção na VM. |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>Iniciar a manutenção em sua instância de VM usando o PowerShell

Você pode iniciar a manutenção em uma VM se **IsCustomerInitiatedMaintenanceAllowed** estiver definido como **true**. Use o cmdlet [set-AzVmss](/powershell/module/az.compute/set-azvmss) com `-PerformMaintenance` parâmetro.

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>Verificar o status de manutenção usando a CLI

Você pode exibir informações de manutenção planejada usando [AZ vmss List-instances](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances).
 
As informações de manutenção serão retornadas somente se a manutenção for planejada. Se nenhuma manutenção que afete a instância de VM estiver agendada, o comando não retornará nenhuma informação de manutenção. 

```azure-cli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

As propriedades a seguir são retornadas em **MaintenanceRedeployStatus** para cada instância de VM: 

| Valor | Descrição   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se você pode iniciar a manutenção na VM no momento. |
| PreMaintenanceWindowStartTime         | O início da janela de autoatendimento de manutenção quando você pode iniciar a manutenção em sua VM. |
| PreMaintenanceWindowEndTime           | O final da janela de autoatendimento de manutenção quando você pode iniciar a manutenção em sua VM. |
| MaintenanceWindowStartTime            | O início da manutenção agendada na qual o Azure inicia a manutenção em sua VM. |
| MaintenanceWindowEndTime              | O fim da janela de manutenção agendada na qual o Azure inicia a manutenção em sua VM. |
| LastOperationResultCode               | O resultado da última tentativa de iniciar a manutenção na VM. |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>Iniciar a manutenção em sua instância de VM usando a CLI

A chamada a seguir inicia a manutenção em uma instância de VM se `IsCustomerInitiatedMaintenanceAllowed` estiver definido como **true**:

```azure-cli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>FAQ

**P: por que você precisa reinicializar minhas VMs agora?**

**R:** Embora a maioria das atualizações e upgrades para a plataforma do Azure não afetem a disponibilidade da VM, em alguns casos, não podemos evitar a reinicialização de VMs hospedadas no Azure. Acumulamos várias alterações que exigem a reinicialização dos nossos servidores, resultando na reinicialização da VM.

**P: se eu seguir as recomendações para alta disponibilidade usando um conjunto de disponibilidade, estou seguro?**

**R:** As máquinas virtuais implantadas em um conjunto de disponibilidade ou em conjuntos de dimensionamento de máquinas virtuais usam domínios de atualização. Ao executar a manutenção, o Azure honra a restrição de domínio de atualização e não reinicia as VMs de um domínio de atualização diferente (dentro do mesmo conjunto de disponibilidade). O Azure também aguarda pelo menos 30 minutos antes de passar para o próximo grupo de VMs. 

Para obter mais informações sobre alta disponibilidade, consulte [regiões e disponibilidade para máquinas virtuais no Azure](../virtual-machines/windows/availability.md).

**P: como posso ser notificado sobre a manutenção planejada?**

**R:** Uma onda de manutenção planejada começa definindo uma agenda para uma ou mais regiões do Azure. Logo após, uma notificação por email é enviada para os proprietários da assinatura (um email por assinatura). Você pode adicionar canais e destinatários para essa notificação usando alertas do log de atividades. Se você implantar uma VM em uma região na qual a manutenção planejada já está agendada, você não receberá a notificação. Em vez disso, verifique o estado de manutenção da VM.

**P: não vejo nenhuma indicação de manutenção planejada no portal, no PowerShell ou na CLI. Qual é o problema?**

**R:** As informações relacionadas à manutenção planejada estão disponíveis durante uma onda de manutenção planejada somente para as VMs afetadas pela manutenção planejada. Se você não vir dados, a onda de manutenção pode já ter sido concluída (ou não iniciada) ou sua VM já pode estar hospedada em um servidor atualizado.

**P: há uma maneira de saber exatamente quando minha VM será afetada?**

**R:** Quando definimos a agenda, definimos uma janela de tempo de vários dias. A sequenciação exata de servidores (e VMs) nesse período é desconhecida. Se você quiser saber o tempo exato em que suas VMs serão atualizadas, poderá usar [eventos agendados](../virtual-machines/windows/scheduled-events.md). Ao usar eventos agendados, você pode consultar de dentro da VM e receber uma notificação de 15 minutos antes de uma reinicialização da VM.

**P: quanto tempo levará para reinicializar minha VM?**

**R:**  Dependendo do tamanho da VM, a reinicialização pode levar até vários minutos durante a janela de manutenção de autoatendimento. Durante as reinicializações iniciadas pelo Azure na janela de manutenção agendada, a reinicialização normalmente leva cerca de 25 minutos. Se você usar serviços de nuvem (função Web/de trabalho), conjuntos de dimensionamento de máquinas virtuais ou conjuntos de disponibilidade, receberá 30 minutos entre cada grupo de VMs (domínio de atualização) durante a janela de manutenção agendada. 

**P: não vejo nenhuma informação de manutenção em minhas VMs. O que deu errado?**

**R:** Há várias razões pelas quais você pode não ver informações de manutenção em suas VMs:
   - Você está usando uma assinatura marcada como *Microsoft Internal*.
   - Suas VMs não estão agendadas para manutenção. Pode ser que a onda de manutenção tenha sido encerrada, cancelada ou modificada para que suas VMs não sejam mais afetadas por ela.
   - Você não tem a coluna de **manutenção** adicionada ao modo de exibição de lista de VM. Embora tenhamos adicionado essa coluna à exibição padrão, se você configurar a exibição para ver colunas não padrão, será necessário adicionar manualmente a coluna **manutenção** à sua exibição de lista de VMs.

**P: minha VM está agendada para manutenção pela segunda vez. Por?**

**R:** Em vários casos de uso, sua VM está agendada para manutenção depois que você já tiver concluído sua manutenção e reimplantada:
   - Cancelamos a onda de manutenção e a reiniciamos com uma carga diferente. Pode ser que detectamos uma carga com falha e simplesmente precisamos implantar uma carga adicional.
   - Sua *VM foi* reparada para outro nó devido a uma falha de hardware.
   - Você optou por parar (desalocar) e reiniciar a VM.
   - Você tem o **desligamento automático** ativado para a VM.

## <a name="next-steps"></a>Passos seguintes

Saiba como registrar-se para eventos de manutenção de dentro da VM usando [eventos agendados](../virtual-machines/windows/scheduled-events.md).
