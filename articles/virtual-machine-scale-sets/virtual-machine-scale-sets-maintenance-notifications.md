---
title: Notificações de manutenção para conjuntos de escala de máquinas virtuais em Azure
description: Ver notificações de manutenção e iniciar a manutenção de autosserviço para conjuntos de escala de máquinas virtuais em Azure.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 08/20/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 5521e49c767a2510bf7c8c53cf6ac5e86b73b466
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87837181"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Planned maintenance notifications for virtual machine scale sets (Notificações de manutenção planeada para conjuntos de dimensionamento de máquinas virtuais)


O Azure executa periodicamente atualizações para melhorar a fiabilidade, desempenho e segurança da infraestrutura hospedeira para máquinas virtuais (VMs). As atualizações podem incluir a correção do ambiente de hospedagem ou a atualização e desmantelamento de hardware. A maioria das atualizações não afeta os VMs hospedados. No entanto, as atualizações afetam os VM nestes cenários:

- Se a manutenção não necessitar de um reboot, o Azure utiliza migração no local para interromper o VM enquanto o hospedeiro é atualizado. As operações de manutenção que não necessitam de um reboot são aplicadas por domínio de avaria. Os progressos são interrompidos se forem recebidos sinais de saúde de aviso.

- Se a manutenção necessitar de um reboot, recebe um aviso de quando a manutenção está planeada. Nestes casos, é-lhe dada uma janela de tempo que normalmente é de 35 dias onde você pode começar a manutenção por si mesmo, quando funciona para si.


A manutenção planeada que requer um reboot está programada em ondas. Cada onda tem âmbito diferente (regiões):

- Uma onda começa com uma notificação aos clientes. Por predefinição, a notificação é enviada ao proprietário da subscrição e aos coproprietários. Pode adicionar destinatários e opções de mensagens como e-mail, SMS e webhooks às notificações utilizando alertas de [Registo de Atividades](../azure-monitor/platform/platform-logs-overview.md)Azure .  
- Com a notificação, é disponibilizada uma *janela de self-service.* Durante esta janela que é tipicamente de 35 dias, você pode encontrar quais dos seus VMs estão incluídos na onda. Pode iniciar proativamente a manutenção de acordo com as suas necessidades de agendamento.
- Após a janela de autosserviço, começa uma *janela de manutenção programada.* Em algum momento durante esta janela, a Azure programa e aplica a manutenção necessária ao seu VM. 

O objetivo de ter duas janelas é dar-lhe tempo suficiente para iniciar a manutenção e reiniciar o seu VM enquanto sabe quando o Azure iniciará automaticamente a manutenção.

Pode utilizar o portal Azure, o PowerShell, a REST API e o Azure CLI para consultar janelas de manutenção para o seu conjunto de vMs de escala de máquina virtual e para iniciar a manutenção de autosserviço.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Deve iniciar a manutenção durante a janela de autosserviço?  

As seguintes diretrizes podem ajudá-lo a decidir se deve iniciar a manutenção no momento em que escolher.

> [!NOTE] 
> A manutenção de autosserviço pode não estar disponível para todos os seus VMs. Para determinar se a recolocação proactiva está disponível para o seu VM, procure **iniciar agora** o estado de manutenção. Atualmente, a manutenção de self-service não está disponível para Azure Cloud Services (Web/Worker Role) e Azure Service Fabric.


A manutenção de autosserviço não é recomendada para implementações que utilizem *conjuntos de disponibilidade*. Os conjuntos de disponibilidade são configurações altamente disponíveis nas quais apenas um domínio de atualização é afetado a qualquer momento. Para conjuntos de disponibilidade:

- Deixe a Azure ativar a manutenção. Para uma manutenção que requer um reboot, a manutenção é feita por domínio de atualização. Os domínios de atualização não recebem necessariamente a manutenção sequencialmente. Há uma pausa de 30 minutos entre os domínios de atualização.
- Se uma perda temporária de alguma da sua capacidade (contagem de domínio de 1/atualização) for uma preocupação, pode facilmente compensar a perda atribuindo instâncias adicionais durante o período de manutenção.
- Para uma manutenção que não requer um reboot, as atualizações são aplicadas ao nível do domínio de avaria. 
    
**Não** utilize a manutenção de autosserviço nos seguintes cenários: 

- Se desligar frequentemente os seus VMs, manualmente, utilizando a DevTest Labs, utilizando o desligamento automático ou seguindo um horário. A manutenção de autosserviço nestes cenários pode reverter o estado de manutenção e causar tempo de inatividade adicional.
- Em VMs de curta duração que você sabe serão apagados antes do fim da onda de manutenção. 
- Para cargas de trabalho com um grande estado armazenado no disco local (efémero) que pretende manter após a atualização. 
- Se redimensionar o seu VM com frequência. Este cenário pode reverter o estado de manutenção. 
- Se tiver adotado eventos programados que permitam o failover proactivo ou o encerramento gracioso da sua carga de trabalho 15 minutos antes do início do encerramento da manutenção.

**Utilize** a manutenção de autosserviço se pretender executar o seu VM ininterruptamente durante a fase de manutenção programada e nenhuma das contraindicações anteriores se aplicar. 

É melhor utilizar a manutenção de autosserviço nos seguintes casos:

- Tem de comunicar uma janela de manutenção exata à administração ou ao seu cliente. 
- Tem de completar a manutenção até uma data específica. 
- É necessário controlar a sequência de manutenção, por exemplo, numa aplicação multi-nível, para garantir uma recuperação segura.
- Precisa de mais de 30 minutos de tempo de recuperação de VM entre dois domínios de atualização. Para controlar o tempo entre os domínios de atualização, tem de acionar a manutenção dos seus VMs, um domínio de atualização de cada vez.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>Ver conjuntos de escala de máquina virtual que são afetados pela manutenção no portal

Quando uma onda de manutenção planeada está programada, pode ver a lista de conjuntos de escala de máquinas virtuais que são afetados pela onda de manutenção que se avizinha utilizando o portal Azure. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu esquerdo, selecione **Todos os serviços**e, em seguida, selecione **conjuntos de escala de máquina virtual**.
3. Em **conjuntos de escala de máquina virtual,** selecione **editar colunas** para abrir a lista de colunas disponíveis.
4. Na secção **colunas disponíveis,** selecione **a manutenção de self-service**e, em seguida, mova-a para a lista de **colunas selecionadas.** Selecione **Aplicar**.  

    Para facilitar a procura do produto **de manutenção self-service,** pode alterar a opção de drop-down na secção **colunas Disponíveis** de **All** to **Properties**.

A coluna **de manutenção de autosserviço** aparece agora na lista de conjuntos de escala de máquinas virtuais. Cada conjunto de escala de máquina virtual pode ter um dos seguintes valores para a coluna de manutenção de autosserviço:

| Valor | Descrição |
|-------|-------------|
| Sim | Pelo menos um VM no seu conjunto de escala de máquina virtual está numa janela de autosserviço. Pode iniciar a manutenção a qualquer momento durante esta janela de autosserviço. | 
| Não | Não há VMs numa janela de autosserviço no conjunto de escala de máquina virtual afetado. | 
| - | Os conjuntos de escala de máquinas virtuais não fazem parte de uma onda de manutenção planeada.| 

## <a name="notification-and-alerts-in-the-portal"></a>Notificação e alertas no portal

O Azure comunica um horário de manutenção planeada enviando um e-mail ao proprietário da assinatura e ao grupo de coproprietários. Pode adicionar destinatários e canais a esta comunicação criando alertas de Registo de Atividade. Para obter mais informações, consulte [a atividade de subscrição do Monitor com o Registo de Atividades Azure](../azure-monitor/platform/platform-logs-overview.md).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No menu esquerdo, selecione **Monitor**. 
3. No painel **Monitor - Alertas (clássicos),** selecione **+Adicione o alerta de registo de atividade**.
4. Na página **de alerta de registo de atividades Adicionar,** selecione ou introduza as informações solicitadas. Em **Critérios,** certifique-se de que define os seguintes valores:
   - **Categoria de evento**: Select **Service Health**.
   - **Serviços**: Selecione **conjuntos de balanças de máquinas virtuais e máquinas virtuais.**
   - **Tipo**: Selecione **a manutenção planeada.** 
    
Para saber mais sobre como configurar alertas de Registo de Atividade, consulte [Alertas de Registo de Atividades](../azure-monitor/platform/activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Inicie a manutenção na escala de máquina virtual definida a partir do portal

Pode ver mais detalhes relacionados com a manutenção na visão geral dos conjuntos de escala de máquinas virtuais. Se pelo menos um VM no conjunto de escala de máquina virtual for incluído na onda de manutenção planeada, uma nova fita de notificação é adicionada perto do topo da página. Selecione a fita de notificação para ir à página **Manutenção.** 

Na página **manutenção,** pode ver qual a instância VM afetada pela manutenção planeada. Para iniciar a manutenção, selecione a caixa de verificação que corresponde ao VM afetado. Em seguida, selecione  **Iniciar a manutenção**.

Depois de iniciar a manutenção, os VMs afetados na sua balança de máquinas virtuais são submetidos a manutenção e estão temporariamente indisponíveis. Se perdeu a janela de autosserviço, ainda pode ver a janela de tempo quando o seu conjunto de escala de máquina virtual será mantido pelo Azure.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Verifique o estado de manutenção utilizando o PowerShell

Pode utilizar o Azure PowerShell para ver quando estão programados VMs nos seus conjuntos de balanças de máquinas virtuais para manutenção. As informações de manutenção planeadas estão disponíveis utilizando o cmdlet [Get-AzVmss](/powershell/module/az.compute/get-azvmss) quando utilizar o `-InstanceView` parâmetro.
 
As informações de manutenção só são devolvidas se a manutenção for planeada. Se não for programada qualquer manutenção que afete a instância VM, o cmdlet não devolve nenhuma informação de manutenção. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

As seguintes propriedades são devolvidas em **ManutençãoRedeployStatus:** 

| Valor | Descrição   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se pode iniciar a manutenção no VM neste momento. |
| PreMaintenanceWindowStartTime         | O início da janela de autosserviço de manutenção quando pode iniciar a manutenção no seu VM. |
| PreMaintenanceWindowEndTime           | O fim da janela de autosserviço de manutenção quando pode iniciar a manutenção no seu VM. |
| ManutençãoWindowStartTime            | O início da manutenção programada na qual a Azure inicia a manutenção no seu VM. |
| ManutençãoWindowEndTime              | O fim da janela programada de manutenção na qual a Azure inicia a manutenção no seu VM. |
| ÚltimaOperaçãoResultCode               | O resultado da última tentativa de iniciar a manutenção no VM. |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>Inicie a manutenção no seu exemplo VM utilizando o PowerShell

Pode iniciar a manutenção num VM se **o IsCustomerInitiatedMaintenanceAllowed** for **verdadeiro**. Utilize o [cmdlet Set-AzVmss](/powershell/module/az.compute/set-azvmss) com `-PerformMaintenance` parâmetro.

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>Verifique o estado de manutenção utilizando o CLI

Pode visualizar as informações de manutenção planeadas utilizando [as listas az vmss](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances).
 
As informações de manutenção só são devolvidas se a manutenção for planeada. Se não for agendada qualquer manutenção que afete a instância VM, o comando não devolve nenhuma informação de manutenção. 

```azurecli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

As seguintes propriedades são devolvidas em **ManutençãoRedeployStatus** para cada instância VM: 

| Valor | Descrição   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se pode iniciar a manutenção no VM neste momento. |
| PreMaintenanceWindowStartTime         | O início da janela de autosserviço de manutenção quando pode iniciar a manutenção no seu VM. |
| PreMaintenanceWindowEndTime           | O fim da janela de autosserviço de manutenção quando pode iniciar a manutenção no seu VM. |
| ManutençãoWindowStartTime            | O início da manutenção programada na qual a Azure inicia a manutenção no seu VM. |
| ManutençãoWindowEndTime              | O fim da janela programada de manutenção na qual a Azure inicia a manutenção no seu VM. |
| ÚltimaOperaçãoResultCode               | O resultado da última tentativa de iniciar a manutenção no VM. |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>Inicie a manutenção no seu exemplo VM utilizando o CLI

A chamada que se segue inicia a manutenção numa instância VM se `IsCustomerInitiatedMaintenanceAllowed` for definida como **verdadeira:**

```azurecli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>FAQ

**P: Por que precisa de reiniciar os meus VMs agora?**

**A:** Embora a maioria das atualizações e atualizações para a plataforma Azure não afetem a disponibilidade de VM, em alguns casos, não podemos evitar reiniciar VMs hospedados em Azure. Acumulámos várias alterações que nos obrigam a reiniciar os nossos servidores que resultarão no reboot de VM.

**P: Se eu seguir as suas recomendações para uma elevada disponibilidade usando um conjunto de disponibilidade, estou seguro?**

**A:** As máquinas virtuais implantadas num conjunto de disponibilidade ou em conjuntos de escala de máquinas virtuais utilizam domínios de atualização. Ao realizar a manutenção, o Azure honra a restrição de domínio de atualização e não reinicia os VMs de um domínio de atualização diferente (dentro do mesmo conjunto de disponibilidade). O Azure também espera pelo menos 30 minutos antes de se mudar para o próximo grupo de VMs. 

Para obter mais informações sobre a elevada disponibilidade, consulte [Regiões e disponibilidade para máquinas virtuais em Azure.](../virtual-machines/availability.md)

**P: Como posso ser notificado sobre a manutenção planeada?**

**A:** Uma onda de manutenção planeada começa por definir um horário para uma ou mais regiões de Azure. Logo a seguir, é enviada uma notificação por e-mail aos proprietários da subscrição (um email por subscrição). Pode adicionar canais e destinatários para esta notificação utilizando alertas de Registo de Atividade. Se colocar um VM numa região em que a manutenção planeada já está programada, não recebe a notificação. Em vez disso, verifique o estado de manutenção do VM.

**P: Não vejo qualquer indicação de manutenção planeada no portal, PowerShell ou CLI. Qual é o problema?**

**A:** As informações relacionadas com a manutenção planeada estão disponíveis durante uma onda de manutenção planeada apenas para os VMs que são afetados pela manutenção planeada. Se não vir dados, a onda de manutenção pode já estar terminada (ou não iniciada), ou o seu VM pode já estar hospedado num servidor atualizado.

**P: Há uma maneira de saber exatamente quando o meu VM será afetado?**

**A:** Quando definimos o horário, definimos uma janela de tempo de vários dias. A sequenciação exata de servidores (e VMs) nesse período é desconhecida. Se quiser saber a hora exata em que os seus VMs serão atualizados, pode utilizar [eventos programados](../virtual-machines/windows/scheduled-events.md). Quando utilizar eventos agendados, pode consultar-se a partir do VM e receber uma notificação de 15 minutos antes de um reboot de VM.

**P: Quanto tempo vai demorar a reiniciar o meu VM?**

**A:**  Dependendo do tamanho do seu VM, o reboot pode demorar até alguns minutos durante a janela de manutenção de autosserviço. Durante as reinicializações iniciadas pelo Azure na janela de manutenção programada, o reboot normalmente demora cerca de 25 minutos. Se utilizar serviços cloud (Função Web/Worker), conjuntos de balanças de máquinas virtuais ou conjuntos de disponibilidade, é-lhe dado 30 minutos entre cada grupo de VMs (domínio de atualização) durante a janela de manutenção programada. 

**P: Não vejo nenhuma informação de manutenção nos meus VMs. O que é que correu mal?**

**A:** Existem várias razões pelas quais poderá não ver nenhuma informação de manutenção nos seus VMs:
   - Está a utilizar uma subscrição marcada como *Microsoft Internal*.
   - Os seus VMs não estão programados para manutenção. Pode ser que a onda de manutenção tenha terminado, foi cancelada, ou foi modificada para que os seus VMs não sejam mais afetados por ela.
   - Não tem a coluna **manutenção** adicionada à sua visualização da lista VM. Embora tenhamos adicionado esta coluna à vista predefinida, se configurar a sua visão para ver colunas não predefinidas, deve adicionar manualmente a coluna **manutenção** à sua vista de lista VM.

**P: O meu VM está programado para manutenção pela segunda vez. Porquê?**

**A:** Em vários casos de utilização, o seu VM está agendado para manutenção depois de já ter concluído a sua manutenção e reafectado:
   - Cancelamos a onda de manutenção e reiniciámo-la com uma carga diferente. Pode ser que tenhamos detetado uma carga defeituosa e precisamos simplesmente de implantar uma carga adicional.
   - O seu VM foi *reparado* em outro nó devido a uma falha de hardware.
   - Escolheu parar (deallocate) e reiniciar o VM.
   - Tem **a paragem automática** ligada para o VM.

## <a name="next-steps"></a>Passos seguintes

Saiba como se inscrever para eventos de manutenção dentro do VM utilizando [eventos agendados.](../virtual-machines/windows/scheduled-events.md)