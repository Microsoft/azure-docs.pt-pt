---
title: Manipular notificações de manutenção para VMs do Windows no Azure | Documentos da Microsoft
description: Ver notificações de manutenção para máquinas de virtuais do Windows em execução no Azure e comece a manutenção self-service.
services: virtual-machines-windows
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/30/2019
ms.author: shants
ms.openlocfilehash: fccc99f78d038a5f96b9dfe01b575dedcdcb4cdc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65405619"
---
# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>Notificações de manutenção de manipulação em planeadas para as máquinas virtuais do Windows

O Azure realiza periodicamente atualizações para melhorar a fiabilidade, o desempenho e a segurança da infraestrutura de anfitrião para máquinas virtuais. As atualizações são as alterações, como o ambiente de alojamento de aplicação de patches ou atualizar e desativar o hardware. A maioria destas atualizações são efetuadas sem nenhum impacto para as máquinas virtuais alojadas. No entanto, existem casos em que as atualizações têm um impacto:

- Se a manutenção não requer um reinício, o Azure utiliza migração no local para colocar em pausa a VM, enquanto o anfitrião está atualizado. Essas operações de manutenção não rebootful são o domínio de falha aplicada por domínio de falha e o progresso é interrompido se qualquer sinais de estado de funcionamento de aviso são recebidas. 

- Se a manutenção requer uma reinicialização, receberá um aviso de quando a manutenção está prevista. Nestes casos, é-lhe dada uma janela de tempo que é normalmente 30 dias em que pode iniciar a manutenção por conta própria, quando ele funciona melhor para si.


Manutenção planeada, que requer uma reinicialização, é agendada em etapas. Cada fase tem escopo diferente (regiões).

- Uma onda começa com uma notificação para os clientes. Por predefinição, a notificação é enviada para o proprietário da subscrição e os coproprietários. Pode adicionar mais opções de mensagens, como e-mail, SMS e Webhooks e os destinatários de notificações através do Azure [alertas de registo de atividade](../../azure-monitor/platform/activity-logs-overview.md).  
- No momento da notificação, um *janela de self-service* é disponibilizado. Durante esta janela que é normalmente de 30 dias, pode encontrar quais das suas máquinas virtuais estão incluídos nessa onda e iniciar proativamente a manutenção, de acordo com suas próprias necessidades de agendamento.
- Depois da janela de self-service, um *janela de manutenção agendada* começa. Em algum momento durante este período, o Azure agendas e aplica-se a manutenção necessária à máquina virtual. 

O objetivo de ter duas janelas é para lhe dar tempo suficiente para iniciar a manutenção e reiniciar a máquina de virtual sabendo quando o Azure irá automaticamente iniciar manutenção.


Pode utilizar o portal do Azure, o PowerShell, a REST API e a CLI para consultar as janelas de manutenção para as suas VMs e iniciar a manutenção self-service.

  
## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Deve iniciar manutenção durante a janela de self-service?  

As seguintes diretrizes devem ajudar a decidir se pretende utilizar esta capacidade e iniciar a manutenção no seu próprio tempo.

> [!NOTE] 
> Manutenção self-service pode não estar disponível para todas as suas VMs. Para determinar se a reimplementação proativa está disponível para a sua VM, procure o **iniciar agora** no estado de manutenção. Manutenção self-service não está atualmente disponível para serviços Cloud (função da Web/trabalho) e o Service Fabric.


Manutenção self-service não é recomendada para implementações que utilizam **conjuntos de disponibilidade** , uma vez que estes são configurações de elevada disponibilidade, onde apenas um domínio de atualização é afetado num determinado momento. 
- Permitir que o Azure acionar a manutenção. Para manutenção que exige reinicialização, tenha em atenção que a manutenção será feita o domínio de atualização por domínio de atualização, que os domínios de atualização não necessariamente recebem a manutenção em seqüência, e que não há uma pausa de 30 minutos entre domínios de atualização. 
- Se uma perda temporária de algumas das sua capacidade (contagem de domínios de atualização/1) é uma preocupação, ele pode facilmente ser compensado de ao alocar instâncias adicionais durante o período de manutenção.
- Para uma manutenção que não necessita de reiniciar, as atualizações são aplicadas ao nível do domínio de falhas.
    
**Não** utilizar manutenção self-service nos seguintes cenários: 
- Se encerrar as VMs com frequência, seja manualmente, usando os laboratórios Dev/Test, usando o encerramento automático ou seguir uma programação, pode reverter o estado de manutenção e, portanto, fazer com que o tempo de inatividade adicional.
- Em VMs de curta duração que saiba será eliminado antes do final da onda de manutenção. 
- Para cargas de trabalho com um grande estado armazenado no disco local (efémeros) que for o pretendido sejam mantidas após a atualização. 
- Nos casos em que redimensiona a sua VM muitas vezes, como ele foi possível reverter o estado de manutenção.
- Se já ADOTARAM o eventos agendados, que permitem a ativação pós-falha proativa ou encerramento correto de sua carga de trabalho, 15 minutos antes do início de desligamento de manutenção

**Utilize** manutenção self-service, se estiver a planear executar a VM sem interrupções durante a fase de manutenção agendada e nenhuma as indicações contra-argumentação mencionadas acima é aplicável. 

É melhor usar a manutenção self-service nos seguintes casos:

- Precisa para comunicar uma janela de manutenção exato para a sua gestão ou o cliente final. 
- Precisa para concluir a manutenção por uma determinada data. 
- Precisa controlar a sequência de manutenção, por exemplo, aplicação de várias camadas para garantir a recuperação de segura.
- Precisa de mais de 30 minutos de tempo de recuperação VM entre dois domínios de atualização (UDs). Para controlar o tempo entre domínios de atualização, tem de acionar manutenção em suas VMs de um domínio de atualização (UD) ao mesmo tempo.

 

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="check-maintenance-status-using-powershell"></a>Verificar o estado de manutenção com o PowerShell

Também pode utilizar o Azure Powershell para ver quando as VMs são agendadas para manutenção. Informações de manutenção planeada estão disponíveis a partir da [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) cmdlet quando utiliza o `-status` parâmetro.
 
Informações de manutenção são devolvidas apenas se há manutenção planeada. Se nenhuma manutenção está agendada que afeta a VM, o cmdlet não devolve quaisquer informações de manutenção. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

```powershell
Get-AzVM -ResourceGroupName rgName -Name vmName -Status
```

As seguintes propriedades são devolvidas em MaintenanceRedeployStatus: 

| Value | Descrição   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se pode iniciar a manutenção na VM neste momento |
| PreMaintenanceWindowStartTime         | O início da janela de manutenção self-service quando pode iniciar a manutenção na sua VM |
| PreMaintenanceWindowEndTime           | Fim da janela de manutenção self-service quando pode iniciar a manutenção na sua VM |
| MaintenanceWindowStartTime            | Início da manutenção agendada no qual o Azure inicia manutenção na sua VM |
| MaintenanceWindowEndTime              | Fim da janela agendadas de manutenção em que o Azure inicia manutenção na sua VM |
| LastOperationResultCode               | O resultado da última tentativa de iniciar a manutenção na VM |



Também pode obter o estado de manutenção para todas as VMs num grupo de recursos usando [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) e não especificar uma VM.
 
```powershell
Get-AzVM -ResourceGroupName rgName -Status
```

A seguinte função de PowerShell usa seu ID de subscrição e imprime uma lista de VMs que se encontram agendadas para manutenção.

```powershell

function MaintenanceIterator
{
    Select-AzSubscription -SubscriptionId $args[0]

    $rgList= Get-AzResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Iniciar a manutenção na sua VM com o PowerShell

Utilizar as informações da função na secção anterior, o seguinte começa a manutenção numa VM se **IsCustomerInitiatedMaintenanceAllowed** está definido como true.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Implementações clássicas

Se ainda tiver VMs herdadas que foram implementadas com o modelo de implementação clássica, pode utilizar o PowerShell para a consulta para VMs e iniciar a manutenção.

Para obter o estado de manutenção de uma VM, escreva:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Para iniciar a manutenção na sua VM clássica, escreva:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```


## <a name="faq"></a>FAQ


**P: Por que precisa reiniciar agora minhas máquinas virtuais?**

**R:** Embora a maioria das atualizações e melhoramentos para a plataforma do Azure não afetar a disponibilidade da máquina virtual, há casos em que nós não é possível evitar o reinício das máquinas virtuais alojadas no Azure. Temos acumulado várias alterações, requerer que o reinício dos nossos servidores que resultarão no reinício das máquinas virtuais.

**P: Se eu seguir as recomendações para elevada disponibilidade através de um conjunto de disponibilidade, estou seguro?**

**R:** As máquinas virtuais implementadas num conjunto de disponibilidade ou em conjuntos de dimensionamento de máquinas virtuais têm a noção de Domínios de Atualização (UD). Quando efetuar a manutenção, o Azure honra a restrição UD e não irá reiniciar as máquinas virtuais de UD diferentes (dentro do mesmo conjunto de disponibilidade).  Azure também tem de aguardar durante, pelo menos, 30 minutos antes de passar para o grupo seguinte de máquinas virtuais. 

Para obter mais informações sobre a elevada disponibilidade, consulte [regiões e disponibilidade para máquinas virtuais no Azure](regions-and-availability.MD).

**P: Como posso ser notificado sobre manutenção planeada?**

**R:** Inicia uma onda de manutenção planeada ao definir uma agenda a um ou mais regiões do Azure. Logo depois, uma notificação por e-mail é enviada para os proprietários de subscrições (um e-mail por subscrição). Canais adicionais e os destinatários para obter esta notificação poderiam ser configurados com alertas de registo de atividade. No caso de implementar uma máquina virtual para uma região em que a manutenção planeada já foi agendada, não irá receber a notificação, mas em vez disso, tem de verificar o estado de manutenção da VM.

**P: Não vejo qualquer indicação de manutenção planeada no portal, Powershell ou na CLI. O que há de errado?**

**R:** Informações relacionadas com a manutenção planeada estão disponíveis durante uma onda de manutenção planeada apenas para as VMs que vão ser afetadas pela mesma. Em outras palavras, se ver dados não, é possível que a onda de manutenção já concluiu (ou não iniciado) ou que sua máquina virtual já esteja alojada num servidor atualizado.

**P: Existe uma forma de saber exatamente quando a minha máquina virtual será afetada?**

**R:** Ao definir a agenda, definimos uma janela de tempo de vários dias. No entanto, a sequenciação exata de servidores (e as VMs) durante esse período é desconhecida. Podem utilizar os clientes que gostariam de saber o tempo exato para as suas VMs [eventos agendados](scheduled-events.md) e consultar a partir da máquina virtual e receber uma notificação de 15 minutos antes de um reinício VM.

**P: O tempo que demora a reiniciar a minha máquina virtual?**

**R:**  Dependendo do tamanho da sua VM, reinício pode demorar vários minutos durante a janela de manutenção self-service. Durante o Azure iniciou reinicializações na janela de manutenção agendada, o reinício demorará, normalmente, cerca de 25 minutos. Tenha em atenção que no caso de usar os serviços Cloud (função da Web/trabalho), conjuntos de dimensionamento de máquinas virtuais ou conjuntos de disponibilidade, terá a chance de 30 minutos entre cada grupo de VMs (UD) durante a janela de manutenção agendada. 

**P: O que é a experiência no caso de conjuntos de dimensionamento de máquinas virtuais?**

**R:** Manutenção planeada já está disponível para os conjuntos de dimensionamento de máquinas virtuais. Para obter instruções sobre como iniciar a manutenção self-service, consulte [manutenção planeada para o VMSS](../../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md) documento.

**P: O que é a experiência no caso de serviços Cloud (função da Web/trabalho) e o Service Fabric?**

**R:** Apesar de estas plataformas serem afetadas pela manutenção planeada, os clientes que utilizam estas plataformas são considerados seguros, uma vez que apenas as VMs num Domínio de Atualização (UD) único serão impactadas em qualquer momento. Manutenção self-service não está atualmente disponível para serviços Cloud (função da Web/trabalho) e o Service Fabric.

**P: Não vejo qualquer informação de manutenção nas minhas VMs. O que aconteceu de errado?**

**R:** Há vários motivos por que não esteja a ver quaisquer informações de manutenção nas suas VMs:
1.  Está a utilizar uma subscrição marcada como Microsoft interna.
2.  As VMs não são agendadas para manutenção. É possível que terminou a onda de manutenção, cancelada ou modificada para que as suas VMs já não são afetadas pela mesma.
3.  Não tem o **manutenção** coluna adicionada à sua exibição de lista VM. Apesar de termos adicionado esta coluna para a exibição padrão, os clientes que configuraram para ver colunas não predefinidas tem de adicionar manualmente os **manutenção** coluna à sua vista de lista VM.

**P: A minha VM está agendada para manutenção pela segunda vez. Porquê?**

**R:** Existem vários casos de utilização em que verá a sua VM agendada para manutenção depois de já ter concluído a reimplementação da manutenção:
1.  Temos cancelada a onda de manutenção e reiniciado-lo com um payload de diferente. É possível que foi detetada payload com falha e precisamos simplesmente de implementar um payload adicional.
2.  A VM esteve *corrigido por outro serviço* para outro nó devido a uma falha de hardware
3.  Selecionou a parar (desaloque) e reinicie a VM
4.  Tiver **encerramento automático** ativado para a VM


## <a name="next-steps"></a>Passos Seguintes

Saiba como pode se registrar para eventos de manutenção a partir de dentro da VM com [eventos agendados](scheduled-events.md).
