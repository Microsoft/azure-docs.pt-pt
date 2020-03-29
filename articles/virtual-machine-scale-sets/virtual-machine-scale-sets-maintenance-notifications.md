---
title: Notificações de manutenção para conjuntos de escala de máquinas virtuais em Azure
description: Ver notificações de manutenção e iniciar a manutenção de self-service para conjuntos de escala de máquinas virtuais em Azure.
author: shants123
tags: azure-service-management,azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: shants
ms.openlocfilehash: 53ebb7c4710c5455ef90701dc7e94f1b846a874a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062692"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Planned maintenance notifications for virtual machine scale sets (Notificações de manutenção planeada para conjuntos de dimensionamento de máquinas virtuais)


O Azure realiza periodicamente atualizações para melhorar a fiabilidade, desempenho e segurança da infraestrutura hospedeira para máquinas virtuais (VMs). As atualizações podem incluir a correção do ambiente de hospedagem ou a atualização e desmantelamento de hardware. A maioria das atualizações não afeta os VMs hospedados. No entanto, as atualizações afetam os VMs nestes cenários:

- Se a manutenção não necessitar de um reboot, o Azure utiliza a migração no local para parar o VM enquanto o hospedeiro é atualizado. As operações de manutenção que não requerem um reboot são aplicadas por domínio de avaria. Os progressos são interrompidos se forem recebidos sinais de saúde de alerta.

- Se a manutenção necessitar de um reboot, você recebe um aviso de quando a manutenção está planeada. Nestes casos, é-lhe dada uma janela temporal que normalmente é de 35 dias onde você pode iniciar a manutenção por si mesmo, quando funciona para si.


A manutenção planeada que requer um reboot está programada em ondas. Cada onda tem um âmbito diferente (regiões):

- Uma onda começa com uma notificação aos clientes. Por predefinição, a notificação é enviada ao proprietário da subscrição e aos coproprietários. Pode adicionar destinatários e opções de mensagens como e-mail, SMS e webhooks às notificações utilizando [alertas](../azure-monitor/platform/platform-logs-overview.md)de Registo de Atividade sinuosos do Azure .  
- Com a notificação, é disponibilizada uma *janela de self-service.* Durante esta janela que é tipicamente de 35 dias, você pode descobrir qual dos seus VMs estão incluídos na onda. Pode iniciar proativamente a manutenção de acordo com as suas próprias necessidades de agendamento.
- Depois da janela de self-service, começa uma janela de *manutenção programada.* Em algum momento durante esta janela, o Azure programa e aplica a manutenção necessária ao seu VM. 

O objetivo em ter duas janelas é dar-lhe tempo suficiente para iniciar a manutenção e reiniciar o seu VM, sabendo quando azure iniciará automaticamente a manutenção.

Pode utilizar o portal Azure, powerShell, rest API e o Azure CLI para consultar janelas de manutenção para os seus VMs de escala de máquina virtual e para iniciar a manutenção do self-service.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Deve começar a manutenção durante a janela de self-service?  

As seguintes diretrizes podem ajudá-lo a decidir se deve iniciar a manutenção num momento que escolha.

> [!NOTE] 
> A manutenção do self-service pode não estar disponível para todos os seus VMs. Para determinar se a reimplantação proactiva está disponível para o seu VM, procure **Start now** no estado de manutenção. Atualmente, a manutenção de self-service não está disponível para Serviços Azure Cloud (Função Web/Trabalhador) e Tecido de Serviço Azure.


A manutenção do self-service não é recomendada para implementações que utilizem *conjuntos*de disponibilidade . Os conjuntos de disponibilidade são configurações altamente disponíveis em que apenas um domínio de atualização é afetado a qualquer momento. Para conjuntos de disponibilidade:

- Deixe o Azure acionar a manutenção. Para a manutenção que requer um reboot, a manutenção é feita domínio de atualização por domínio de atualização. Os domínios de atualização não recebem necessariamente a manutenção sequencialmente. Há uma pausa de 30 minutos entre domínios de atualização.
- Se uma perda temporária de alguma da sua capacidade (1/contagem de domínio de atualização) for uma preocupação, pode facilmente compensar a perda atribuindo instâncias adicionais durante o período de manutenção.
- Para a manutenção que não requer um reboot, as atualizações são aplicadas ao nível do domínio da falha. 
    
**Não** utilize a manutenção do self-service nos seguintes cenários: 

- Se desligar frequentemente os seus VMs, manualmente, utilizando o DevTest Labs, utilizando a paragem automática ou seguindo um horário. A manutenção do self-service nestes cenários pode reverter o estado de manutenção e causar tempo de inatividade adicional.
- Em VMs de curta duração que você sabe que serão eliminados antes do fim da onda de manutenção. 
- Para cargas de trabalho com um grande estado armazenado no disco local (efémero) que pretende manter após a atualização. 
- Se redimensionar o seu VM com frequência. Este cenário pode reverter o estado de manutenção. 
- Se tiver adotado eventos programados que permitam uma falha proactiva ou um encerramento gracioso da sua carga de trabalho 15 minutos antes do início do encerramento da manutenção.

**Utilize** a manutenção do self-service se pretender executar o seu VM ininterruptamente durante a fase de manutenção programada e nenhuma das contraindicações anteriores se aplica. 

É melhor usar a manutenção do self-service nos seguintes casos:

- Precisa comunicar uma janela de manutenção exata à gerência ou ao seu cliente. 
- Precisa completar a manutenção por uma data específica. 
- É necessário controlar a sequência de manutenção, por exemplo, numa aplicação de vários níveis, para garantir uma recuperação segura.
- Precisa de mais de 30 minutos de tempo de recuperação vm entre dois domínios de atualização. Para controlar o tempo entre os domínios da atualização, deve acionar a manutenção dos seus VMs num domínio de atualização de cada vez.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>Ver conjuntos de escala de máquinas virtuais que são afetados pela manutenção no portal

Quando uma onda de manutenção planeada está programada, pode ver a lista de conjuntos de escala de máquinas virtuais que são afetados pela próxima onda de manutenção utilizando o portal Azure. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No menu esquerdo, selecione **Todos os serviços,** e, em seguida, selecione **conjuntos**de escala de máquina virtual .
3. Em conjuntos de escala de **máquina virtual,** selecione **colunas Editar** para abrir a lista de colunas disponíveis.
4. Na secção **colunas disponíveis,** selecione **manutenção de self-service**e, em seguida, mova-a para a lista de **colunas Selecionadas.** Selecione **Aplicar**.  

    Para facilitar a descoberta do artigo **de manutenção self-service,** pode alterar a opção de drop-down na secção de **colunas Disponíveis** de **All** to **Properties**.

A coluna **de manutenção self-service** aparece agora na lista de conjuntos de escala de máquinas virtuais. Cada conjunto de escala de máquina virtual pode ter um dos seguintes valores para a coluna de manutenção de self-service:

| Valor | Descrição |
|-------|-------------|
| Sim | Pelo menos um VM no seu conjunto de máquinavirtual está numa janela de self-service. Pode iniciar a manutenção a qualquer momento durante esta janela de self-service. | 
| Não | Não há VMs numa janela de self-service no conjunto de escala virtual de máquinas afetada. | 
| - | Os conjuntos de escala de máquinas virtuais não fazem parte de uma onda de manutenção planeada.| 

## <a name="notification-and-alerts-in-the-portal"></a>Notificação e alertas no portal

O Azure comunica um calendário para a manutenção planeada enviando um e-mail ao proprietário da subscrição e ao grupo de coproprietários. Pode adicionar destinatários e canais a esta comunicação criando alertas de Registo de Atividade. Para mais informações, consulte a [atividade de subscrição do Monitor com o Registo de Atividades do Azure](../azure-monitor/platform/platform-logs-overview.md).

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No menu esquerdo, selecione **Monitor**. 
3. No **painel Monitor - Alertas (clássicos),** selecione **+Adicionar alerta**de registo de atividade .
4. Na página de alerta de registo de **atividade adicionar,** selecione ou introduza as informações solicitadas. Em **Critérios,** certifique-se de que define os seguintes valores:
   - **Categoria de evento**: Seleção **de Saúde do Serviço**.
   - **Serviços**: Selecione conjuntos de **escala de máquinavirtual e máquinas virtuais**.
   - **Tipo**: Selecione **manutenção planeada**. 
    
Para saber mais sobre como configurar alertas de Registo de Atividades, consulte Criar alertas de [Registo de Atividades](../azure-monitor/platform/activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Inicie a manutenção na sua escala de máquina virtual a partir do portal

Pode ver mais detalhes relacionados com a manutenção na visão geral dos conjuntos de escala de máquinas virtuais. Se pelo menos um VM no conjunto de escala de máquina virtual estiver incluído na onda de manutenção planeada, uma nova fita de notificação é adicionada perto do topo da página. Selecione a fita de notificação para ir à página **de Manutenção.** 

Na página **de Manutenção,** pode ver qual a instância vm afetada pela manutenção planeada. Para iniciar a manutenção, selecione a caixa de verificação correspondente ao VM afetado. Em seguida, selecione **Iniciar a manutenção**.

Depois de iniciar a manutenção, os VMs afetados na sua escala de máquina virtual passam por manutenção e estão temporariamente indisponíveis. Se perdeu a janela de self-service, ainda pode ver a janela do tempo quando o seu conjunto de escala de máquina virtual será mantido pelo Azure.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Verifique o estado da manutenção utilizando o PowerShell

Pode utilizar o Azure PowerShell para ver quando estão programados os VMs nos seus conjuntos de escala de máquinavirtual para manutenção. As informações de manutenção planeadas estão disponíveis utilizando o cmdlet `-InstanceView` [Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss) quando utilizar o parâmetro.
 
As informações de manutenção só são devolvidas se a manutenção estiver prevista. Se não estiver prevista qualquer manutenção que afete a instância VM, o cmdlet não devolve nenhuma informação de manutenção. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

As seguintes propriedades são devolvidas sob o Estatuto de **Reimplantação de Manutenção:** 

| Valor | Descrição   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se pode iniciar a manutenção no VM neste momento. |
| Pré-ManutençãoWindowStartTime         | O início da janela de self-service de manutenção quando pode iniciar a manutenção no seu VM. |
| PreManutençãoWindowEndTime           | A extremidade da janela de self-service de manutenção quando pode iniciar a manutenção no seu VM. |
| ManutençãoWindowStartTime            | O início da manutenção programada em que o Azure inicia a manutenção no seu VM. |
| ManutençãoWindowEndTime              | O fim da janela programada de manutenção na qual o Azure inicia a manutenção no seu VM. |
| LastOperationResultCode               | O resultado da última tentativa de iniciar a manutenção no VM. |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>Inicie a manutenção na sua instância VM utilizando powerShell

Pode iniciar a manutenção num VM se **o IsCustomerIniciardMaintenanceAllowed** estiver definido como **verdadeiro**. Utilize o [cmdlet Set-AzVmss](/powershell/module/az.compute/set-azvmss) com `-PerformMaintenance` parâmetro.

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>Verifique o estado da manutenção utilizando o CLI

Pode visualizar informações de manutenção planeadas utilizando [instâncias da lista az vmss](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances).
 
As informações de manutenção só são devolvidas se a manutenção estiver prevista. Se não estiver prevista qualquer manutenção que afete a instância VM, o comando não devolve nenhuma informação de manutenção. 

```azurecli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

As seguintes propriedades são devolvidas sob o Estatuto de **Reimplantação de Manutenção** para cada instância VM: 

| Valor | Descrição   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se pode iniciar a manutenção no VM neste momento. |
| Pré-ManutençãoWindowStartTime         | O início da janela de self-service de manutenção quando pode iniciar a manutenção no seu VM. |
| PreManutençãoWindowEndTime           | A extremidade da janela de self-service de manutenção quando pode iniciar a manutenção no seu VM. |
| ManutençãoWindowStartTime            | O início da manutenção programada em que o Azure inicia a manutenção no seu VM. |
| ManutençãoWindowEndTime              | O fim da janela programada de manutenção na qual o Azure inicia a manutenção no seu VM. |
| LastOperationResultCode               | O resultado da última tentativa de iniciar a manutenção no VM. |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>Inicie a manutenção na sua instância VM utilizando o CLI

A chamada seguinte inicia a manutenção numa `IsCustomerInitiatedMaintenanceAllowed` instância VM se for definida como **verdadeira:**

```azurecli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>FAQ

**P: Por que precisa reiniciar os meus VMs agora?**

**A:** Embora a maioria das atualizações e atualizações para a plataforma Azure não afetem a disponibilidade de VM, em alguns casos, não podemos evitar reiniciar VMs hospedados no Azure. Acumulámos várias alterações que nos obrigam a reiniciar os nossos servidores que resultarão no reboot de VM.

**P: Se eu seguir as suas recomendações para alta disponibilidade usando um conjunto de disponibilidade, estou seguro?**

**A:** As máquinas virtuais implantadas num conjunto de disponibilidade ou em conjuntos de escala de máquinas virtuais utilizam domínios de atualização. Ao realizar a manutenção, o Azure honra a restrição de domínio da atualização e não reinicia Os MD de um domínio de atualização diferente (dentro do mesmo conjunto de disponibilidade). Azure também aguarda pelo menos 30 minutos antes de passar para o próximo grupo de VMs. 

Para obter mais informações sobre a elevada disponibilidade, consulte [Regiões e disponibilidade para máquinas virtuais em Azure.](../virtual-machines/windows/availability.md)

**P: Como posso ser notificado sobre a manutenção planeada?**

**A:** Uma onda de manutenção planeada começa por definir um horário para uma ou mais regiões azure. Pouco depois, uma notificação por e-mail é enviada aos proprietários de subscrições (um e-mail por subscrição). Pode adicionar canais e destinatários para esta notificação utilizando alertas de Registo de Atividades. Se implementar um VM para uma região em que a manutenção planeada já está agendada, não recebe a notificação. Em vez disso, verifique o estado de manutenção do VM.

**P: Não vejo qualquer indicação de manutenção planeada no portal, PowerShell ou no CLI. Qual é o problema?**

**A:** As informações relacionadas com a manutenção planeada estão disponíveis durante uma onda de manutenção planeada apenas para os VMs que são afetados pela manutenção planeada. Se não vir dados, a onda de manutenção pode já estar terminada (ou não iniciada), ou o seu VM pode já estar hospedado num servidor atualizado.

**P: Existe uma maneira de saber exatamente quando o meu VM será afetado?**

**A:** Quando definimos o horário, definimos uma janela temporal de vários dias. A sequenciação exata de servidores (e VMs) nesse período é desconhecida. Se quiser saber a hora exata em que os seus VMs serão atualizados, pode utilizar [os eventos agendados.](../virtual-machines/windows/scheduled-events.md) Quando utilizar os eventos programados, pode consultar a partir de dentro do VM e receber uma notificação de 15 minutos antes de um reboot vm.

**P: Quanto tempo vai demorar a reiniciar o meu VM?**

**A:**  Dependendo do tamanho do seu VM, o reboot pode demorar até vários minutos durante a janela de manutenção do self-service. Durante as reinicializações iniciadas pelo Azure na janela de manutenção programada, o reboot normalmente demora cerca de 25 minutos. Se utilizar os Serviços cloud (Função Web/Trabalhador), conjuntos de escala de máquinavirtual ou conjuntos de disponibilidade, é-lhe dado 30 minutos entre cada grupo de VMs (domínio de atualização) durante a janela de manutenção programada. 

**P: Não vejo nenhuma informação de manutenção nos meus VMs. O que é que correu mal?**

**A:** Existem várias razões pelas quais poderá não ver nenhuma informação de manutenção nos seus VMs:
   - Está a utilizar uma subscrição marcada como *Microsoft Internal*.
   - Os seus VMs não estão programados para manutenção. Pode ser que a onda de manutenção tenha terminado, tenha sido cancelada, ou modificada para que os seus VMs não sejam mais afetados por ela.
   - Não tem a coluna de **manutenção** adicionada à sua visão da lista VM. Apesar de termos adicionado esta coluna à vista predefinida, se configurar a sua visão para ver colunas não predefinidas, tem de adicionar manualmente a coluna **de Manutenção** à sua visão da lista VM.

**P: O meu VM está agendado para manutenção pela segunda vez. Porquê?**

**A:** Em vários casos de utilização, o seu VM está programado para manutenção depois de já ter concluído a sua manutenção e redistribuído:
   - Cancelamos a onda de manutenção e reiniciámo-la com uma carga útil diferente. Pode ser que detetámos uma carga útil defeituosa e precisamos simplesmente de descarregar uma carga adicional.
   - O seu VM foi *servido a* outro nó devido a uma falha de hardware.
   - Selecionou parar (desalocar) e reiniciar o VM.
   - Tens **uma paragem automática** ligada para o VM.

## <a name="next-steps"></a>Passos seguintes

Saiba como se registar para eventos de manutenção a partir do VM utilizando [eventos programados](../virtual-machines/windows/scheduled-events.md).
