---
title: Configure Uato De Arranque/Paragem de VMs durante o horário de folga
description: Este artigo diz como configurar os VMs iniciar/parar durante o off-hours para suportar diferentes casos ou cenários de utilização.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 127c924da44c7e596d93b21d89ff4591a90ba7cf
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83827680"
---
# <a name="configure-startstop-vms-during-off-hours"></a>Configure VMs de início/paragem durante o horário de folga

Este artigo descreve como configurar os [VMs iniciar/parar durante o fora de horas](automation-solution-vm-management.md) para suportar os cenários descritos. Também pode aprender a:

* [Configurar as notificações por e-mail](#configure-email-notifications)
* [Adicionar uma VM](#add-a-vm)
* [Excluir uma VM](#exclude-a-vm)
* [Modificar os horários de arranque e encerramento](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>Cenário 1: Start/Stop VMs em horário

Este cenário é a configuração predefinida quando implanta os VMs Start/Stop pela primeira vez durante o horário de folga. Por exemplo, pode configurar a funcionalidade para parar todos os VMs através de uma subscrição quando sair do trabalho à noite, e iniciá-los de manhã quando estiver de volta ao escritório. Quando configuraos os horários **Programados-StartVM** e **Scheduled-StopVM** durante a implementação, iniciam e param os VMs direcionados. 

A configuração da funcionalidade para parar vMs é suportada. Consulte [modificar os horários de arranque e encerramento](#modify-the-startup-and-shutdown-schedules) para aprender a configurar um horário personalizado.

> [!NOTE]
> O fuso horário utilizado pela funcionalidade é o seu fuso horário atual quando configura o parâmetro de tempo de programação. No entanto, a Azure Automation armazena-o em formato UTC na Automação Azure. Não é necessário fazer qualquer conversão de fuso horário, pois isto é manipulado durante a implantação da máquina.

Para controlar os VMs que estão no âmbito, configure as variáveis: `External_Start_ResourceGroupNames` , `External_Stop_ResourceGroupNames` e `External_ExcludeVMNames` .

Pode permitir que se direcione a ação contra um grupo de subscrição e recursos, ou direcione uma lista específica de VMs, mas não ambos.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Direcione as ações de início e paragem contra um grupo de subscrição e recursos

1. Configure as `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` e variáveis para especificar os VMs-alvo.

2. Ative e atualize os horários **de Arranque Agendadoe** e **Paragem Programada-StopVM.**

3. Execute o **livro de** ScheduledStartStop_Parent com o campo de parâmetroS **ACTION** definido para **iniciar** e o campo de parâmetro **sif o que se** aplica ao True para pré-visualizar as suas alterações.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Direcione a ação de início e paragem pela lista VM

1. Executar o **livro de** ScheduledStartStop_Parent **com** o ACTION definido para **começar,** adicione uma lista separada de VMs no campo de parâmetros **VMList** e, em seguida, coloque o campo de **parâmetros WHATIF** para True. Pré-visualizar as suas alterações.

2. Configure a `External_ExcludeVMNames` variável com uma lista separada de VMs (VM1, VM2, VM3).

3. Este cenário não honra as `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupnames` e variáveis. Para este cenário, é necessário criar o seu próprio horário de Automação. Para mais detalhes, consulte [Agendar um livro de redações em Automação Azure.](shared-resources/schedules.md)

    > [!NOTE]
    > O valor para nomes do Grupo de **Recursos-Alvo** é armazenado como os valores para ambos `External_Start_ResourceGroupNames` e `External_Stop_ResourceGroupNames` . Para maior granularidade, pode modificar cada uma destas variáveis para direcionar diferentes grupos de recursos. Para iniciar a ação, utilize `External_Start_ResourceGroupNames` e utilize para parar a `External_Stop_ResourceGroupNames` ação. Os VMs são automaticamente adicionados aos horários de início e paragem.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Cenário 2: Iniciar/Parar VMS em sequência utilizando tags

Num ambiente que inclui dois ou mais componentes em múltiplas VMs suportando uma carga de trabalho distribuída, suportando a sequência em que os componentes são iniciados e parados por ordem é importante. 

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Direcione as ações de início e paragem contra um grupo de subscrição e recursos

1. Adicione uma `sequencestart` etiqueta `sequencestop` com valores inteiros positivos aos VMs que são direcionados `External_Start_ResourceGroupNames` e `External_Stop_ResourceGroupNames` variáveis. As ações de início e paragem são realizadas em ordem ascendente. Para aprender a marcar um VM, consulte [a Tag a Windows virtual machine in Azure](../virtual-machines/windows/tag.md) e [marque uma máquina virtual Linux em Azure](../virtual-machines/linux/tag.md).

2. Modifique os horários **Sequenciados-StartVM** e **Sequenced-StopVM** até à data e hora que satisfazem os seus requisitos e ativem o horário.

3. Execute o **livro de SequencedStartStop_Parent** com o ACTION definido para **começar** e **o WHATIF** definido para True para pré-visualizar as suas alterações. **ACTION**

4. Pré-visualizar a ação e fazer as alterações necessárias antes de implementar contra os VMs de produção. Quando estiver pronto, execute manualmente o livro de execução com o parâmetro definido para **Falso,** ou deixe que os horários de Automatização **Sequenciados-StartVM** e **Sequenced-StopVM** corram automaticamente seguindo o horário prescrito.

### <a name="target-the-start-and-stop-actions-by-vm-list"></a>Direcione as ações de início e paragem por lista VM

1. Adicione uma `sequencestart` etiqueta `sequencestop` com valores inteiros positivos aos VMs que pretende adicionar ao `VMList` parâmetro.

2. Executar o **livro de** execução SequencedStartStop_Parent **com** action set para **iniciar,** adicionar uma lista separada de VMs no campo de parâmetros **VMList** e, em seguida, definir **O IFIF** para True. Pré-visualizar as suas alterações.

3. Configure a `External_ExcludeVMNames` variável com uma lista separada de VMs (VM1, VM2, VM3).

4. Este cenário não honra as `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupnames` e variáveis. Para este cenário, é necessário criar o seu próprio horário de Automação. Para mais detalhes, consulte [Agendar um livro de redações em Automação Azure.](shared-resources/schedules.md)

5. Pré-visualizar a ação e fazer as alterações necessárias antes de implementar contra os VMs de produção. Quando estiver pronto, execute manualmente o livro de **monitorização e diagnóstico/monitorização-grupos de ação** com o parâmetro definido para **Falso**. Em alternativa, deixe os horários de Automatização **Sequenciados-StartVM** e **Sequenced-StopVM** executarem automaticamente seguindo o horário prescrito.

## <a name="scenario-3-start-or-stop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Cenário 3: Iniciar ou parar automaticamente com base na utilização do CPU

Os VMs de arranque/paragem durante o horário de folga podem ajudar a gerir o custo de funcionamento do Gestor de Recursos Azure e vMs clássicos na sua subscrição, avaliando máquinas que não são usadas durante períodos não-pico, como horas fora de horas, e desligando-as automaticamente se a utilização do processador for inferior a uma percentagem especificada.

Por padrão, a funcionalidade é pré-configurada para avaliar a métrica percentual do CPU para ver se a utilização média é de 5% ou menos. Este cenário é controlado pelas seguintes variáveis e pode ser modificado se os valores predefinidos não cumprirem os seus requisitos:

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

Pode ativar e direcionar a ação contra um grupo de subscrição e recursos, ou visar uma lista específica de VMs.

Ao executar o **livro de AutoStop_CreateAlert_Parent,** verifica que existem a subscrição direcionada, o grupo de recursos e os VMs. Se os VMs existirem, o livro de execução chama o **AutoStop_CreateAlert_Child** livro de execução para cada VM verificado pelo livro-mãe. Este livro infantil:

* Cria uma regra de alerta métrico para cada VM verificado.
* Aciona o **AutoStop_VM_Child** livro de execução para um VM específico se o CPU descer abaixo do limiar configurado para o intervalo de tempo especificado. 
* Tenta parar o VM.

### <a name="target-the-autostop-action-against-all-vms-in-a-subscription"></a>Direcione a ação de paragem automática contra todos os VMs numa subscrição

1. Certifique-se de que a `External_Stop_ResourceGroupNames` variável está vazia ou definida para * (wildcard).

2. [Opcional] Se quiser excluir alguns VMs da ação de paragem automática, pode adicionar uma lista separada de nomes vma à `External_ExcludeVMNames` variável.

3. Ative o calendário **Schedule_AutoStop_CreateAlert_Parent** a funcionar para criar as regras de alerta métrica stop VM necessárias para todos os VMs na sua subscrição. Executar este tipo de horário permite criar novas regras de alerta métrico à medida que novos VMs são adicionados à subscrição.

### <a name="target-the-autostop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>Direcione a ação de paragem automática contra todos os VMs de um grupo de recursos ou múltiplos grupos de recursos

1. Adicione uma lista separada de nomes de grupos de recursos à `External_Stop_ResourceGroupNames` variável.

2. Se quiser excluir alguns dos VMs do autostop, pode adicionar uma lista separada de nomes vma à `External_ExcludeVMNames` variável.

3. Ative o calendário **Schedule_AutoStop_CreateAlert_Parent** a funcionar para criar as regras de alerta métrica stop VM necessárias para todos os VMs dos seus grupos de recursos. Executar esta operação num horário permite-lhe criar novas regras de alerta métrico, à medida que são adicionados novos VMs ao ou grupo de recursos.

### <a name="target-the-autostop-action-to-a-list-of-vms"></a>Direcione a ação de paragem automática para uma lista de VMs

1. Crie um novo [horário](shared-resources/schedules.md#create-a-schedule) e ligue-o ao **livro de AutoStop_CreateAlert_Parent,** adicionando uma lista separada de nomes vma ao `VMList` parâmetro.

2. Opcionalmente, se quiser excluir alguns VMs da ação de paragem automática, pode adicionar uma lista separada de nomes vma à `External_ExcludeVMNames` variável.

## <a name="configure-email-notifications"></a>Configurar as notificações por e-mail

Para alterar as notificações de e-mail após a implementação de VMs start/stop durante o off-hours, pode modificar o grupo de ação criado durante a implementação.  

> [!NOTE]
> As subscrições na nuvem do Governo Azure não suportam a funcionalidade de e-mail desta funcionalidade.

1. No portal Azure, navegue para **monitor,** em **seguida, grupos de ação.** Selecione o grupo de ação chamado **StartStop_VM_Notication**.

    ![Página de Gestão de Atualização de Automatização](media/automation-solution-vm-management/azure-monitor.png)

2. Na página StartStop_VM_Notification, clique em **Editar detalhes** em **Detalhes**. Isto abre a página email/SMS/Push/Voice. Atualize o endereço de e-mail e clique **em OK** para guardar as suas alterações.

    ![Página de Gestão de Atualização de Automatização](media/automation-solution-vm-management/change-email.png)

    Alternativamente, você pode adicionar ações adicionais ao grupo de ação, para saber mais sobre grupos de ação, ver [grupos de ação](../azure-monitor/platform/action-groups.md)

Segue-se um e-mail de exemplo que é enviado quando a funcionalidade desliga as máquinas virtuais.

![Página de Gestão de Atualização de Automatização](media/automation-solution-vm-management/email.png)

## <a name="add-or-exclude-vms"></a><a name="add-exclude-vms"></a>Adicionar ou excluir VMs

A funcionalidade permite-lhe adicionar VMs para ser direcionado ou excluído. 

### <a name="add-a-vm"></a>Adicionar uma VM

Existem duas formas de garantir que um VM é incluído quando a funcionalidade é executado:

* Cada um dos [livros-mãe](automation-solution-vm-management.md#runbooks) da funcionalidade tem um `VMList` parâmetro. Pode passar uma lista separada de nomes vm separados para este parâmetro ao agendar o livro de execução dos pais apropriado para a sua situação, e estes VMs serão incluídos quando a funcionalidade for executada.

* Para selecionar vários VMs, detete e com os nomes do grupo de `External_Start_ResourceGroupNames` recursos que `External_Stop_ResourceGroupNames` contenham os VMs que pretende iniciar ou parar. Também pode definir as variáveis como um valor de ter a funcionalidade executada contra todos os grupos de `*` recursos na subscrição.

### <a name="exclude-a-vm"></a>Excluir uma VM

Para excluir um VM de VMs stop/start durante o horário de folga, pode adicionar o seu nome à `External_ExcludeVMNames` variável. Esta variável é uma lista separada de VMs específicos para excluir da funcionalidade. Esta lista está limitada a 140 VMs. Se adicionar mais de 140 VMs a esta lista, os VMs que estão definidos para serem excluídos podem ser inadvertidamente iniciados ou parados.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Modificar os horários de arranque e encerramento

Gerir os horários de arranque e encerramento nesta funcionalidade segue os mesmos passos que delineados na Agenda um livro de [execução em Automação Azure.](shared-resources/schedules.md) São necessários horários separados para iniciar e parar VMs.

Configurar a funcionalidade para parar vMs num determinado momento é suportado. Neste cenário basta criar um horário de paragem e nenhum horário de início correspondente. 

1. Certifique-se de que adicionou os grupos de recursos para que os VMs se desliguem na `External_Stop_ResourceGroupNames` variável.

2. Crie o seu próprio horário para o momento em que quer desligar os VMs.

3. Navegue para o **livro de ScheduledStartStop_Parent** e clique em **Agendar**. Isto permite-lhe selecionar o horário que criou no passo anterior.

4. **Selecione Parâmetros e executar definições** e defina o campo **ACTION** para **parar**.

5. Selecione **OK** para guardar as alterações.

## <a name="next-steps"></a>Passos seguintes

* Para monitorizar a funcionalidade durante o funcionamento, consulte os [registos de Consulta dos VMs iniciar/parar durante as horas de folga](automation-solution-vm-management-logs.md).
* Para lidar com problemas durante a gestão da VM, consulte [Os VMs Start/Stop de Troubleshoot durante as questões fora de horas](troubleshoot/start-stop-vm.md).
