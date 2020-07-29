---
title: Configurar Azure Automation Start/Stop VMs durante as horas de folga
description: Este artigo diz como configurar os VMs iniciar/parar durante o período de folga para suportar diferentes casos ou cenários de utilização.
services: automation
ms.subservice: process-automation
ms.date: 06/01/2020
ms.topic: conceptual
ms.openlocfilehash: 3fbd6292f654071f74b4dfccc5e4de393ccfff02
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84266721"
---
# <a name="configure-startstop-vms-during-off-hours"></a>Configurar VMs de início/paragem durante as horas fora de horas

Este artigo descreve como configurar os [VMs iniciar/parar durante o](automation-solution-vm-management.md) período de folga para suportar os cenários descritos. Também pode aprender a:

* [Configurar as notificações por e-mail](#configure-email-notifications)
* [Adicionar uma VM](#add-a-vm)
* [Excluir uma VM](#exclude-a-vm)
* [Modifique os horários de arranque e de paragem](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>Cenário 1: VMs de início/paragem em um horário

Este cenário é a configuração predefinida quando implementa vMs de início/paragem durante o horário de folga. Por exemplo, pode configurar a funcionalidade para parar todos os VMs através de uma subscrição quando sai do trabalho à noite, e começar de manhã quando estiver de volta ao escritório. Quando configura os horários **Programados-StartVM** e **Programado-StopVM** durante a implementação, eles iniciam e param os VMs direcionados. 

Configurar a funcionalidade para apenas parar VMs é suportado. Consulte [modificar os horários de arranque e de paragem](#modify-the-startup-and-shutdown-schedules) para aprender a configurar um horário personalizado.

> [!NOTE]
> O fuso horário utilizado pela funcionalidade é o seu fuso horário atual quando configura o parâmetro de horário do horário. No entanto, a Azure Automation armazena-a em formato UTC na Azure Automation. Não é preciso fazer qualquer conversão de fuso horário, pois isto é manuseado durante a colocação da máquina.

Para controlar os VMs que estão no âmbito, configurar as variáveis: `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` , e `External_ExcludeVMNames` .

Pode ativar a ação contra um grupo de subscrição e recursos, ou direcionar uma lista específica de VMs, mas não ambos.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Direcione as ações de partida e de paragem contra um grupo de subscrição e recursos

1. Configure as `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` variáveis e variáveis para especificar os VMs-alvo.

2. Ativar e atualizar os **horários programados de StartVM** e **De Paragem Programada.**

3. Executar o **ScheduledStartStop_Parent** livro de aplicação com o campo de **parâmetros ACTION** definido para **iniciar** e o campo de **parâmetros WHATIF** definido para True para visualizar as suas alterações.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Direcione a ação inicial e stop pela lista VM

1. Executar o **ScheduledStartStop_Parent** runbook com **AÇÃO** definido para **começar**.

2. Adicione uma lista separada de VMs (sem espaços) no campo de parâmetros **VMList.** Uma lista de exemplos é `vm1,vm2,vm3` .

3. Desa esta qual é o campo de **parâmetros WHATIF** para True.

4. Configure a `External_ExcludeVMNames` variável com uma lista separada por vMs (VM1,VM2,VM3), sem espaços entre valores separados por vírgula.

5. Este cenário não honra as `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupnames` variáveis e variáveis. Para este cenário, precisa de criar o seu próprio horário de Automação. Para mais informações, consulte [um livro de recortes na Azure Automation](shared-resources/schedules.md).

    > [!NOTE]
    > O valor para **nomes do Grupo de Recursos-Alvo** é armazenado como valores para ambos `External_Start_ResourceGroupNames` e `External_Stop_ResourceGroupNames` . Para obter mais granularidade, pode modificar cada uma destas variáveis para direcionar diferentes grupos de recursos. Para iniciar a ação, `External_Start_ResourceGroupNames` utilize-se e `External_Stop_ResourceGroupNames` use-o para parar a ação. Os VMs são automaticamente adicionados aos horários de início e paragem.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Cenário 2: Iniciar/Parar VMS em sequência utilizando tags

Num ambiente que inclua dois ou mais componentes em vários VMs suportando uma carga de trabalho distribuída, apoiar a sequência em que os componentes são iniciados e parados em ordem é importante. 

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Direcione as ações de partida e de paragem contra um grupo de subscrição e recursos

1. Adicione uma `sequencestart` e uma etiqueta com `sequencestop` valores inteiros positivos aos VMs que são direcionados `External_Start_ResourceGroupNames` e `External_Stop_ResourceGroupNames` variáveis. As ações de início e de paragem são realizadas por ordem crescente. Para aprender a marcar um VM, consulte [uma máquina virtual Windows em Azure](../virtual-machines/windows/tag.md) e marque uma máquina virtual [Linux em Azure](../virtual-machines/linux/tag.md).

2. Modifique os horários **Sequenciais-StartVM** e **Sequencial-StopVM** para a data e hora que satisfaçam os seus requisitos e ative o horário.

3. Execute o **SequencedStartStop_Parent** runbook com **ACTION** definido para **iniciar** e **OIF** definido para True para visualizar as suas alterações.

4. Pré-visualizar a ação e fazer quaisquer alterações necessárias antes de implementar contra os VMs de produção. Quando estiver pronto, execute manualmente o livro de bordo com o parâmetro definido para **Falso**, ou deixe que os horários de Automatização **Sequenciados-StartVM** e **Sequenciais-StopVM** executem automaticamente seguindo o horário prescrito.

### <a name="target-the-start-and-stop-actions-by-vm-list"></a>Direcione as ações de início e paragem pela lista VM

1. Adicione uma `sequencestart` e uma etiqueta com `sequencestop` valores inteiros positivos aos VMs que pretende adicionar ao `VMList` parâmetro.

2. Executar o **SequencedStartStop_Parent** runbook com **AÇÃO** definido para **começar**.

3. Adicione uma lista separada de VMs (sem espaços) no campo de parâmetros **VMList.** Uma lista de exemplos é `vm1,vm2,vm3` .

4. **Desafasar o WHATIF** para o Verdadeiro. 

5. Configure a `External_ExcludeVMNames` variável com uma lista separada por vms, sem espaços entre valores separados por vírgula.

6. Este cenário não honra as `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupnames` variáveis e variáveis. Para este cenário, precisa de criar o seu próprio horário de Automação. Para mais informações, consulte [um livro de recortes na Azure Automation](shared-resources/schedules.md).

7. Pré-visualizar a ação e fazer quaisquer alterações necessárias antes de implementar contra os VMs de produção. Quando estiver pronto, execute manualmente o **livro de grupos de monitorização e diagnóstico/monitorização-acção-grupo** com o parâmetro definido para **Falso**. Em alternativa, deixe que os horários de Automatização **sequenciados-StartVM** e **Sequenciais-StopVM** sequenciem automaticamente seguindo o horário prescrito.

## <a name="scenario-3-start-or-stop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Cenário 3: Iniciar ou parar automaticamente com base na utilização do CPU

Os VMs de arranque/paragem durante as horas de folga podem ajudar a gerir o custo de funcionamento do Azure Resource Manager e os VMs clássicos na sua subscrição, avaliando máquinas que não são utilizadas durante períodos não-pico, como horas de expediente, e desligando-as automaticamente se a utilização do processador for inferior a uma percentagem especificada.

Por padrão, a funcionalidade é pré-configurada para avaliar a percentagem da métrica do CPU para ver se a utilização média é de 5% ou menos. Este cenário é controlado pelas seguintes variáveis e pode ser modificado se os valores padrão não cumprirem os seus requisitos:

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

Pode ativar e direcionar a ação contra um grupo de subscrição e recursos, ou direcionar uma lista específica de VMs.

Quando executam o **AutoStop_CreateAlert_Parent** runbook, verifica-se que existem subscrições direcionadas, grupos de recursos e VMs. Se os VMs existirem, o livro de recortes chama o **AutoStop_CreateAlert_Child** livro de bordo para cada VM verificado pelo livro de bordo dos pais. Este livro infantil:

* Cria uma regra de alerta métrico para cada VM verificado.
* Aciona o **AutoStop_VM_Child** livro de bordo de um determinado VM se o CPU descer abaixo do limiar configurado para o intervalo de tempo especificado. 
* Tentativas de parar o VM.

### <a name="target-the-autostop-action-against-all-vms-in-a-subscription"></a>Direcione a ação de autostop contra todos os VMs numa subscrição

1. Certifique-se de que a `External_Stop_ResourceGroupNames` variável está vazia ou definida para * (wildcard).

2. [Opcional] Se pretender excluir alguns VMs da ação de auto-paragem, pode adicionar uma lista separada de vm à `External_ExcludeVMNames` variável.

3. Ative o horário **de Schedule_AutoStop_CreateAlert_Parent** para criar as regras de alerta métrica stop VM necessárias para todos os VMs na sua subscrição. A execução deste tipo de horário permite criar novas regras de alerta métrico à medida que novos VMs são adicionados à subscrição.

### <a name="target-the-autostop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>Direcione a ação autostop contra todos os VMs num grupo de recursos ou em vários grupos de recursos

1. Adicione uma lista separada de vírgulas de nomes de grupos de recursos à `External_Stop_ResourceGroupNames` variável.

2. Se quiser excluir alguns dos VMs do autostop, pode adicionar uma lista separada de vm à `External_ExcludeVMNames` variável.

3. Ativar o horário **de Schedule_AutoStop_CreateAlert_Parent** para criar as regras de alerta métricas stop VM necessárias para todos os VMs dos seus grupos de recursos. Executar esta operação num horário permite criar novas regras de alerta métrico à medida que novos VMs são adicionados ao grupo de recursos(s).

### <a name="target-the-autostop-action-to-a-list-of-vms"></a>Direcione a ação de autostop para uma lista de VMs

1. Crie um novo [horário](shared-resources/schedules.md#create-a-schedule) e ligue-o ao **AutoStop_CreateAlert_Parent** runbook, adicionando uma lista separada de vm de nomes VM ao `VMList` parâmetro.

2. Opcionalmente, se pretender excluir alguns VMs da ação de autostop, pode adicionar uma lista separada de vm de nomes VM (sem espaços) à `External_ExcludeVMNames` variável.

## <a name="configure-email-notifications"></a>Configurar as notificações por e-mail

Para alterar notificações de e-mail após a implementação de VMs de início/paragem durante o período de folga, pode modificar o grupo de ação criado durante a implementação.  

> [!NOTE]
> As subscrições na nuvem do Governo Azure não suportam a funcionalidade de e-mail desta funcionalidade.

1. No portal Azure, navegue para **Monitor,** depois **grupos de ação.** Selecione o grupo de ação chamado **StartStop_VM_Notication**.

    ![Página de Gestão de Atualização de Automação](media/automation-solution-vm-management/azure-monitor.png)

2. Na página StartStop_VM_Notification, clique em **Editar detalhes** em **Detalhes**. Isto abre a página Email/SMS/Push/Voice. Atualize o endereço de e-mail e clique **em OK** para guardar as suas alterações.

    ![Página de Gestão de Atualização de Automação](media/automation-solution-vm-management/change-email.png)

    Em alternativa, pode adicionar ações adicionais ao grupo de ação, para saber mais sobre grupos de ação, ver [grupos de ação](../azure-monitor/platform/action-groups.md)

Segue-se um e-mail de exemplo que é enviado quando a funcionalidade desliga as máquinas virtuais.

![Página de Gestão de Atualização de Automação](media/automation-solution-vm-management/email.png)

## <a name="add-or-exclude-vms"></a><a name="add-exclude-vms"></a>Adicionar ou excluir VMs

A funcionalidade permite-lhe adicionar VMs para serem direcionados ou excluídos. 

### <a name="add-a-vm"></a>Adicionar uma VM

Existem duas formas de garantir que um VM seja incluído quando a funcionalidade é executado:

* Cada um dos livros de bordo dos [pais](automation-solution-vm-management.md#runbooks) da funcionalidade tem um `VMList` parâmetro. Pode passar uma lista separada de vm de nomes VM (sem espaços) para este parâmetro ao agendar o livro de aplicação dos pais apropriado para a sua situação, e estes VMs serão incluídos quando a funcionalidade for executada.

* Para selecionar vários VMs, definir `External_Start_ResourceGroupNames` e `External_Stop_ResourceGroupNames` com os nomes de grupo de recursos que contêm os VMs que pretende iniciar ou parar. Também pode definir as variáveis para um valor de `*` ter a funcionalidade executada contra todos os grupos de recursos na subscrição.

### <a name="exclude-a-vm"></a>Excluir uma VM

Para excluir um VM de VM de Stop/start VMs durante o horário de folga, pode adicionar o seu nome à `External_ExcludeVMNames` variável. Esta variável é uma lista separada por vms específicos (sem espaços) para excluir da funcionalidade. Esta lista está limitada a 140 VMs. Se adicionar mais de 140 VMs a esta lista, os VMs que estão programados para serem excluídos podem ser inadvertidamente iniciados ou parados.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Modifique os horários de arranque e de paragem

Gerir os horários de arranque e de paragem nesta funcionalidade segue os mesmos passos que delineado na [Agenda um runbook na Azure Automation](shared-resources/schedules.md). São necessários horários separados para iniciar e parar os VMs.

Configurar a funcionalidade para apenas parar VMs em determinado momento é suportado. Neste cenário basta criar um crono de paragem e nenhum calendário de início correspondente. 

1. Certifique-se de que adicionou os grupos de recursos para os VMs desligarem na `External_Stop_ResourceGroupNames` variável.

2. Crie o seu próprio horário para a hora em que quiser desligar os VMs.

3. Navegue para o **ScheduledStartStop_Parent** livro de regras e clique **em Agendar**. Isto permite-lhe selecionar o horário que criou no passo anterior.

4. Selecione **parâmetros e executar as definições** e definir o campo **ACTION** para **parar**.

5. Selecione **OK** para guardar as alterações.

## <a name="next-steps"></a>Próximos passos

* Para monitorizar a função durante o funcionamento, consulte [os registos de consulta dos VMs de início/paragem durante as horas de folga](automation-solution-vm-management-logs.md).
* Para lidar com problemas durante a gestão de VM, consulte [os VMs de início/paragem de resolução de problemas durante as horas fora de horas](troubleshoot/start-stop-vm.md).
