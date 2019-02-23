---
title: Conjuntos de notificações de manutenção para o dimensionamento de máquinas virtuais no Azure | Documentos da Microsoft
description: Ver notificações de manutenção e iniciar a manutenção self-service para conjuntos de dimensionamento de máquinas virtuais no Azure.
services: virtual-machine-scale-sets
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2018
ms.author: shants
ms.openlocfilehash: 3d3ed7d1448ecb350a75a933666e6ab8bd17e84c
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737217"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Notificações de manutenção planeada para conjuntos de dimensionamento de máquinas virtuais


O Azure efetua periodicamente atualizações para melhorar a fiabilidade, desempenho e segurança da infraestrutura do anfitrião para máquinas virtuais (VMs). As atualizações podem incluir o ambiente de alojamento de aplicação de patches ou atualizar e desativar o hardware. A maioria das atualizações não afetam as VMs alojadas. No entanto, as atualizações afetam VMs nestes cenários:

- Se a manutenção não requer um reinício, o Azure utiliza migração no local para colocar em pausa a VM, enquanto o anfitrião está atualizado. Operações de manutenção que não requerem um reinício são o domínio de falha aplicada por domínio de falha. Curso é interrompido se qualquer sinais de estado de funcionamento de aviso são recebidas.

- Se a manutenção requer uma reinicialização, receberá uma notificação que mostra quando está prevista a manutenção. Nestes casos, é-lhe dada uma janela de tempo em que pode iniciar a manutenção por conta própria quando ele funciona melhor para si.


Manutenção planeada, que requer uma reinicialização é agendada em etapas. Cada fase tem escopo diferente (regiões):

- Uma onda começa com uma notificação para os clientes. Por predefinição, a notificação é enviada para o proprietário da subscrição e os coproprietários. Pode adicionar destinatários e opções de mensagens, como e-mail, SMS e webhooks para notificações ao utilizar o Azure [alertas de registo de atividades](../azure-monitor/platform/activity-logs-overview.md).  
- Com a notificação, um *janela de self-service* é disponibilizado. Durante este período, pode encontrar o que as VMs estão incluídas na onda. Pode iniciar proativamente a manutenção, de acordo com suas necessidades de agendamento.
- Depois da janela de self-service, um *janela de manutenção agendada* começa. Em algum momento durante este período, o Azure agendas e aplica-se a manutenção necessária para a VM. 

O objetivo de ter duas janelas é para lhe dar tempo suficiente para iniciar a manutenção e a sua VM sabendo quando o Azure irá automaticamente iniciar manutenção.

Pode utilizar o portal do Azure, PowerShell, a API REST e a CLI do Azure para consultar as janelas de manutenção para VMs do conjunto de dimensionamento de máquinas virtuais e para iniciar a manutenção self-service.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Deve iniciar manutenção durante a janela de self-service?  

As diretrizes seguintes podem ajudar a decidir se pretende iniciar a manutenção num momento que escolher.

> [!NOTE] 
> Manutenção self-service pode não estar disponível para todas as suas VMs. Para determinar se a reimplementação proativa está disponível para a sua VM, procure **iniciar agora** no estado de manutenção. Atualmente, manutenção self-service não está disponível para serviços Cloud do Azure (função da Web/trabalho) e o Azure Service Fabric.


Manutenção self-service não é recomendada para implementações que utilizem *conjuntos de disponibilidade*. Conjuntos de disponibilidade são configurações de elevada disponibilidade na qual atualização apenas um domínio é afetado em qualquer altura. Para conjuntos de disponibilidade:

- Permitir que o Azure acionar a manutenção. Para uma manutenção que requer uma reinicialização, manutenção é feita o domínio de atualização por domínio de atualização. Domínios de atualização não necessariamente recebem sequencialmente a manutenção. Há uma pausa de 30 minutos entre domínios de atualização.
- Se uma perda temporária de algumas das sua capacidade (contagem de domínios de atualização/1) é uma preocupação, pode facilmente compensar a perda ao alocar instâncias adicionais durante o período de manutenção.
- Para uma manutenção que não requer um reinício, as atualizações são aplicadas ao nível do domínio de falhas. 
    
**Não** utilizar manutenção self-service nos seguintes cenários: 

- Se encerrar as VMs com frequência, manualmente, usando os laboratórios DevTest, seja através da utilização de encerramento automático ou seguindo uma agenda. Manutenção self-service nesses cenários pode reverter o estado de manutenção e fazer com que o tempo de inatividade adicional.
- Em VMs de curta duração que saiba será eliminado antes do final da onda de manutenção. 
- Para cargas de trabalho com um grande estado armazenado no disco local (efémeros) que pretende manter após a atualização. 
- Se o redimensionamento, muitas vezes, a VM. Este cenário poderá reverter o estado de manutenção. 
- Se já ADOTARAM o eventos agendados, que permitem proativa ativação pós-falha ou encerramento correto de sua carga de trabalho de 15 minutos antes do início do encerramento de manutenção.

**Fazer** utilizar manutenção self-service, se planear executar a VM sem interrupções durante a fase de manutenção agendada e nenhuma dos counterindications anteriores aplicar. 

É melhor usar a manutenção self-service nos seguintes casos:

- Precisa para comunicar uma janela de manutenção exato para gerenciamento ou seu cliente. 
- Precisa para concluir a manutenção até uma data específica. 
- Precisa controlar a sequência de manutenção, por exemplo, num aplicativo de várias camado, para garantir a recuperação de segura.
- Precisa de mais de 30 minutos de tempo de recuperação VM entre dois domínios de atualização. Para controlar o tempo entre domínios de atualização, tem de acionar manutenção no seu domínio de atualização de um de VMs ao mesmo tempo.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>Conjuntos de dimensionamento de máquina virtual de vista que são afetados pela manutenção no portal

Quando estiver agendada uma onda de manutenção planeada, pode ver a lista de conjuntos de dimensionamento de máquinas virtuais que são afetados pela onda de manutenção futura com o portal do Azure. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu da esquerda, selecione **todos os serviços**e, em seguida, selecione **conjuntos de dimensionamento de máquinas virtuais**.
3. Sob **conjuntos de dimensionamento de máquinas virtuais**, selecione **editar colunas** para abrir a lista de colunas disponíveis.
4. Na **colunas disponíveis** secção, selecione **manutenção self-service**, e, em seguida, mova-o para o **nas colunas selecionadas** lista. Selecione **Aplicar**.  

    Para tornar o **manutenção self-service** item mais fácil para localizar, pode alterar a opção de menu pendente na **colunas disponíveis** secção da **todos os** para  **Propriedades**.

O **manutenção self-service** coluna agora aparece na lista de conjuntos de dimensionamento de máquina virtual. Cada conjunto de dimensionamento de máquina virtual pode ter um dos seguintes valores para a coluna de manutenção self-service:

| Value | Descrição |
|-------|-------------|
| Sim | Pelo menos uma VM no conjunto de dimensionamento de máquina virtual está numa janela de self-service. Pode iniciar a manutenção em qualquer altura durante esta janela de self-service. | 
| Não | Não existem VMs estão numa janela de self-service no dimensionamento de máquinas de virtuais afetadas definidas. | 
| - | O dimensionamento de máquinas virtuais conjuntos não fazem parte de uma onda de manutenção planeada.| 

## <a name="notification-and-alerts-in-the-portal"></a>Notificações e alertas no portal

Azure comunica um agendamento para a manutenção planeada ao enviar um e-mail para o grupo proprietário e os coproprietários de subscrição. Pode adicionar os destinatários e canais para esta comunicação através da criação de alertas de registo de atividades. Para obter mais informações, consulte [monitorizar a atividade de subscrição com o registo de atividades do Azure](../azure-monitor/platform/activity-logs-overview.md).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu da esquerda, selecione **Monitor**. 
3. Na **monitorizar - alertas (clássico)** painel, selecione **+ Adicionar alerta de registo de atividade**.
4. Sobre o **Adicionar alerta de registo de atividade** página, selecione ou introduza as informações pedidas. Na **critérios**, certifique-se de que defina os seguintes valores:
   - **Categoria de evento**: Selecione **estado de funcionamento do serviço**.
   - **Serviços**: Selecione **conjuntos de dimensionamento de máquinas virtuais e máquinas virtuais**.
   - **Tipo de**: Selecione **manutenção planeada**. 
    
Para saber mais sobre como configurar alertas de registo de atividades, consulte [alertas de criar o registo de atividades](../azure-monitor/platform/activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Iniciar manutenção no seu conjunto de dimensionamento de máquina virtual do portal

Pode ver mais detalhes relacionados com a manutenção na descrição geral dos conjuntos de dimensionamento de máquina virtual. Se pelo menos uma VM no conjunto de dimensionamento de máquina virtual está incluída na onda de manutenção planeada, é adicionada uma nova faixa de opções de notificação perto da parte superior da página. Selecione a faixa de opções de notificação para ir para o **manutenção** página. 

Sobre o **manutenção** página, pode ver a instância VM é afetada pela manutenção planeada. Para iniciar a manutenção, selecione a caixa de verificação que corresponde à VM afetada. Em seguida, selecione **iniciar manutenção**.

Depois de iniciar a manutenção, as VMs afetadas no seu conjunto de dimensionamento de máquina virtual entrar em manutenção e estão temporariamente indisponíveis. Se perdeu a janela de self-service, pode ainda vir a janela de tempo quando o conjunto de dimensionamento de máquina virtual será mantido pelo Azure.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Verificar o estado de manutenção com o PowerShell

Pode utilizar o Azure PowerShell para ver quando as VMs nos seus conjuntos de dimensionamento de máquina virtual estão agendadas para manutenção. Informações de manutenção planeada estão disponíveis ao utilizar o [Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss) cmdlet quando utiliza o `-InstanceView` parâmetro.
 
Informações de manutenção são devolvidas apenas se a manutenção está prevista. Se nenhuma manutenção está agendada que afeta a instância VM, o cmdlet não devolve quaisquer informações de manutenção. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

As seguintes propriedades são devolvidas em **MaintenanceRedeployStatus**: 
| Value | Descrição   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se pode iniciar a manutenção na VM neste momento. ||
| PreMaintenanceWindowStartTime         | O início da janela de manutenção self-service quando pode iniciar a manutenção na sua VM. ||
| PreMaintenanceWindowEndTime           | O fim da janela de manutenção self-service quando pode iniciar a manutenção na sua VM. ||
| MaintenanceWindowStartTime            | O início da manutenção agendada, em que o Azure inicia manutenção na sua VM. ||
| MaintenanceWindowEndTime              | O fim da janela agendadas de manutenção em que o Azure inicia manutenção na sua VM. ||
| LastOperationResultCode               | O resultado da última tentativa de iniciar a manutenção na VM. ||



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>Iniciar a manutenção em sua instância de VM com o PowerShell

Pode iniciar a manutenção numa VM se **IsCustomerInitiatedMaintenanceAllowed** está definida como **verdadeiro**. Utilize o [Set-AzVmss](/powershell/module/az.compute/set-azvmss) cmdlet com `-PerformMaintenance` parâmetro.

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>Verificar o estado de manutenção com a CLI

Pode ver informações de manutenção planeada utilizando [az vmss-instâncias de lista](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances).
 
Informações de manutenção são devolvidas apenas se a manutenção está prevista. Se nenhuma manutenção que afeta a instância VM é agendada, o comando não devolve quaisquer informações de manutenção. 

```azure-cli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

As seguintes propriedades são devolvidas em **MaintenanceRedeployStatus** para cada instância VM: 
| Value | Descrição   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se pode iniciar a manutenção na VM neste momento. ||
| PreMaintenanceWindowStartTime         | O início da janela de manutenção self-service quando pode iniciar a manutenção na sua VM. ||
| PreMaintenanceWindowEndTime           | O fim da janela de manutenção self-service quando pode iniciar a manutenção na sua VM. ||
| MaintenanceWindowStartTime            | O início da manutenção agendada, em que o Azure inicia manutenção na sua VM. ||
| MaintenanceWindowEndTime              | O fim da janela agendadas de manutenção em que o Azure inicia manutenção na sua VM. ||
| LastOperationResultCode               | O resultado da última tentativa de iniciar a manutenção na VM. ||


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>Iniciar a manutenção em sua instância de VM com a CLI

A seguinte chamada inicia manutenção numa instância de VM, se `IsCustomerInitiatedMaintenanceAllowed` está definido como **true**:

```azure-cli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>FAQ

**P: Por que precisa reiniciar agora minhas VMs?**

**R:** Embora a maioria das atualizações e melhoramentos para a plataforma do Azure não afetam a disponibilidade da VM, em alguns casos, não podemos não é possível evitar o reinício de VMs alojadas no Azure. Temos acumulado várias alterações, requerer que o reinício dos nossos servidores que resultarão no reinício da VM.

**P: Se eu seguir as suas recomendações para elevada disponibilidade através da utilização de disponibilidade do conjunto, sou eu seguro?**

**R:** Máquinas virtuais implementadas num disponibilidade definida ou em conjuntos de dimensionamento de máquina virtual utilizar domínios de atualização. Quando efetuar a manutenção, o Azure honra a restrição de domínio de atualização e não reiniciar VMs a partir de um domínio de atualização diferentes (dentro do mesmo conjunto de disponibilidade). Azure também tem de aguardar durante, pelo menos, 30 minutos antes de passar para o grupo seguinte de VMs. 

Para obter mais informações sobre a elevada disponibilidade, consulte [regiões e disponibilidade para máquinas virtuais no Azure](../virtual-machines/windows/regions-and-availability.md).

**P: Como posso ser notificado sobre manutenção planeada?**

**R:** Inicia uma onda de manutenção planeada ao definir uma agenda a um ou mais regiões do Azure. Logo depois, uma notificação por e-mail é enviada para os proprietários de subscrições (um e-mail por subscrição). Pode adicionar canais e os destinatários para obter esta notificação ao utilizar os alertas de registo de atividades. Se implementar uma VM para uma região em que já foi agendada a manutenção planeada, não receber a notificação. Em vez disso, verifique o estado de manutenção da VM.

**P: Não vejo qualquer indicação de manutenção planeada no portal, PowerShell ou a CLI. O que há de errado?**

**R:** Informações relacionadas com a manutenção planeada está disponível durante uma onda de manutenção planeada apenas para as VMs que são afetados pela manutenção planeada. Se não vir dados, a onda de manutenção pode já estar concluída (ou não iniciado), ou a VM já pode ser alojada num servidor atualizado.

**P: Existe uma forma de saber exatamente quando a minha VM será afetada?**

**R:** Quando vamos definir a agenda, definimos uma janela de tempo de vários dias. A sequenciação exata de servidores (e VMs) nesse período é desconhecida. Se deseja saber o tempo exato, serão possível atualizar as suas VMs, pode utilizar [eventos agendados](../virtual-machines/windows/scheduled-events.md). Quando usa eventos agendados, pode consultar a partir de dentro da VM e receber uma notificação de 15 minutos antes de um reinício VM.

**P: O tempo que demora a reiniciar a minha VM?**

**R:**  Dependendo do tamanho da sua VM, o reinício pode demorar vários minutos durante a janela de manutenção self-service. Durante os reinícios iniciada pelo Azure na janela de manutenção agendada, a reinicialização normalmente demora cerca de 25 minutos. Se utiliza os serviços de Cloud (função da Web/trabalho), os conjuntos de dimensionamento de máquinas virtuais ou conjuntos de disponibilidade, é-lhe dada 30 minutos entre cada grupo de VMs (domínio de atualização) durante a janela de manutenção agendada. 

**P: Não vejo qualquer informação de manutenção nas minhas VMs. O que aconteceu de errado?**

**R:** Há vários motivos por que razão poderá não ver todas as informações de manutenção nas suas VMs:
   - Estiver a utilizar uma subscrição marcada como *Microsoft Internal*.
   - As VMs não estão agendadas para manutenção. Pode ser que a onda de manutenção terminou, foi cancelada ou foi modificada para que as suas VMs já não são afetadas pelo mesmo.
   - Não tem o **manutenção** coluna adicionada à sua exibição de lista VM. Embora esta coluna, adicionámos a exibição padrão, se configurar a vista para ver colunas não predefinidas, tem de adicionar manualmente os **manutenção** coluna à sua exibição de lista VM.

**P: A minha VM está agendada para manutenção pela segunda vez. Porquê?**

**R:** Em vários casos de utilização, a sua VM está agendada para manutenção depois de já ter concluído a manutenção e implantados novamente:
   - Temos cancelada a onda de manutenção e reiniciado-lo com um payload de diferente. Pode ser que foi detetada uma carga com falha e precisamos simplesmente de implementar um payload adicional.
   - A VM esteve *corrigido por outro serviço* para outro nó devido a uma falha de hardware.
   - Selecionou a parar (desaloque) e reinicie a VM.
   - Tiver **encerramento automático** ativado para a VM.

## <a name="next-steps"></a>Passos Seguintes

Saiba como registar-se para eventos de manutenção a partir de dentro da VM utilizando [eventos agendados](../virtual-machines/windows/scheduled-events.md).
