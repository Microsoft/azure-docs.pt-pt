---
title: Configurar a solução de VMs start/stop
description: Este artigo descreve como configurar os VMs de início/paragem durante a solução off-hours para suportar diferentes casos ou cenários de utilização.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: d3ca8d17d6637f0ab2b5a5d3d7a99ac0beaafd2e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550386"
---
# <a name="how-to-configure-startstop-vms-during-off-hours-solution"></a>Como configurar Os VMs start/stop durante a solução de horas de folga

Com os VMs iniciar/parar durante a solução de horas de folga, pode:

- [Agende VMs para começar e parar.](#schedule)
- Agendar VMs para iniciar e parar em ordem ascendente [usando Tags Azure](#tags) (não suportados para VMs clássicos).
- VMs de paragem automática com base na [baixa utilização do CPU](#cpuutil).

Este artigo descreve como configurar com sucesso a solução para suportar estes cenários. Também pode aprender a executar outras configurações comuns de configuração para a solução, tais como:

* [Configurar as notificações por e-mail](#configure-email-notifications)

* [Adicionar uma VM](#add-a-vm)

* [Excluir uma VM](#exclude-a-vm)

* [Modificar os horários de arranque e encerramento](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>Cenário 1: Start/Stop VMs em horário

Este cenário é a configuração padrão quando se implementa a solução pela primeira vez. Por exemplo, pode configurá-lo para parar todos os VMs através de uma subscrição quando sair do trabalho à noite, e iniciá-los de manhã quando estiver de volta ao escritório. Quando configuraos os horários **Programados-StartVM** e **Scheduled-StopVM** durante a implementação, iniciam e param os VMs direcionados. Configurar esta solução para parar vMs é suportado, consulte [Modificar os horários de arranque e encerramento](#modify-the-startup-and-shutdown-schedules) para aprender a configurar um horário personalizado.

> [!NOTE]
> O fuso horário é o seu fuso horário atual quando configura o parâmetro de tempo de programação. No entanto, encontra-se armazenado em formato UTC na Automação Azure. Não é necessário fazer qualquer conversão de fuso horário, uma vez que esta é manipulada durante a implantação.

Controla quais os VMs que estão no âmbito configurando as seguintes variáveis: **External_Start_ResourceGroupNames,** **External_Stop_ResourceGroupNames**e **External_ExcludeVMNames**.

Pode permitir que se direcione a ação contra um grupo de subscrição e recursos, ou direcione uma lista específica de VMs, mas não ambos.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Direcione as ações de início e paragem contra um grupo de subscrição e recursos

1. Configure as variáveis **External_Stop_ResourceGroupNames** e **External_ExcludeVMNames** para especificar os VMs-alvo.

2. Ative e atualize os horários **de Arranque Agendadoe** e **Paragem Programada-StopVM.**

3. Execute o **livro de ScheduledStartStop_Parent** com o parâmetro ACTION definido para **iniciar** e o parâmetro WHATIF definido para **True** para pré-visualizar as suas alterações.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Direcione a ação de início e paragem pela lista VM

1. Executar o **livro de** ScheduledStartStop_Parent com o parâmetro ACTION definido para **iniciar,** adicionar uma lista separada de VMs no parâmetro *VMList* e, em seguida, definir o parâmetro WHATIF para **True**. Pré-visualizar as suas alterações.

2. Configure o parâmetro **External_ExcludeVMNames** com uma lista separada de VMs (VM1, VM2, VM3).

3. Este cenário não honra as variáveis **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupnames.** Para este cenário, é necessário criar o seu próprio horário de Automação. Para mais detalhes, consulte [Agendar um livro de execução em Automação Azure.](../automation/automation-schedules.md)

    > [!NOTE]
    > O valor para nomes de **grupode recursos-alvo** é armazenado como o valor tanto para **External_Start_ResourceGroupNames** como para **External_Stop_ResourceGroupNames**. Para maior granularidade, pode modificar cada uma destas variáveis para direcionar diferentes grupos de recursos. Para iniciar a ação, utilize **External_Start_ResourceGroupNames**, e para parar a ação, utilize **External_Stop_ResourceGroupNames**. Os VMs são automaticamente adicionados aos horários de início e paragem.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Cenário 2: Iniciar/Parar VMS em sequência utilizando tags

Num ambiente que inclui dois ou mais componentes em múltiplas VMs suportando uma carga de trabalho distribuída, suportando a sequência em que os componentes são iniciados e parados por ordem é importante. Pode realizar este cenário executando os seguintes passos:

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Direcione as ações de início e paragem contra um grupo de subscrição e recursos

1. Adicione um **início** de sequência e uma etiqueta de **sequência** stop com um valor inteiro positivo para VMs que são direcionados em **variáveis External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames.** As ações de início e paragem são realizadas em ordem ascendente. Para aprender a marcar um VM, consulte [a Tag a Windows Virtual Machine em Azure](../virtual-machines/windows/tag.md) e [marque uma máquina virtual Linux em Azure](../virtual-machines/linux/tag.md).

2. Modifique os horários **Sequenciados-StartVM** e **Sequenced-StopVM** até à data e hora que satisfazem os seus requisitos e ativem o horário.

3. Execute o **livro de SequencedStartStop_Parent** com o parâmetro ACTION definido para **iniciar** e o parâmetro WHATIF definido para **True** para pré-visualizar as suas alterações.

4. Pré-visualizar a ação e fazer as alterações necessárias antes de implementar contra os VMs de produção. Quando estiver pronto, execute manualmente o livro de execução com o parâmetro definido para **Falso,** ou deixe que o programade Automação **Sequenced-StartVM** e **Sequenced-StopVM** corra automaticamente seguindo o horário prescrito.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Direcione a ação de início e paragem pela lista VM

1. Adicione um arranque de **sequência** e uma etiqueta de **sequência** stop com um valor inteiro positivo aos VMs que planeia adicionar ao parâmetro **VMList.**

2. Executar o **livro de** execução SequencedStartStop_Parent com o parâmetro ACTION definido para **iniciar,** adicione uma lista separada de VMs no parâmetro *VMList* e, em seguida, coloque o parâmetro WHATIF para **True**. Pré-visualizar as suas alterações.

3. Configure o parâmetro **External_ExcludeVMNames** com uma lista separada de VMs (VM1, VM2, VM3).

4. Este cenário não honra as variáveis **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupnames.** Para este cenário, é necessário criar o seu próprio horário de Automação. Para mais detalhes, consulte [Agendar um livro de execução em Automação Azure.](../automation/automation-schedules.md)

5. Pré-visualizar a ação e fazer as alterações necessárias antes de implementar contra os VMs de produção. Quando estiver pronto, execute manualmente o manual de monitorização e diagnóstico/monitorização-grupos de ação com o parâmetro definido para **Falso,** ou deixe que o horário de automatização **Sequenciado-StartVM** e **Sequenced-StopVM** corra automaticamente seguindo o horário prescrito.

## <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Cenário 3: Iniciar/Parar automaticamente com base na utilização do CPU

Esta solução pode ajudar a gerir o custo de funcionamento do Gestor de Recursos Azure e das máquinas virtuais Clássicas na sua subscrição, avaliando VMs que não são utilizados durante períodos não-pico, como horas fora de horas, e desligando-os automaticamente se a utilização do processador for inferior a uma percentagem especificada.

Por padrão, a solução é pré-configurada para avaliar a métrica percentual do CPU para ver se a utilização média é de 5% ou menos. Este cenário é controlado pelas seguintes variáveis e pode ser modificado se os valores predefinidos não cumprirem os seus requisitos:

* **External_AutoStop_MetricName**

* **External_AutoStop_Threshold**

* **External_AutoStop_TimeAggregationOperator**

* **External_AutoStop_TimeWindow**

* **External_AutoStop_Frequency**

* **External_AutoStop_Severity**

Pode ativar e direcionar a ação contra um grupo de subscrição e recursos, ou visar uma lista específica de VMs.

Ao executar o **livro de** AutoStop_CreateAlert_Parent, verifica que a subscrição direcionada, os grupos de recursos e os VMs existem. Se os VMs existirem, então chama o **AutoStop_CreateAlert_Child** livro de execução para cada VM verificado pelo livro-mãe. Este livro infantil executa o seguinte:

* Cria uma regra de alerta métrico para cada VM verificado.

* Aciona o **AutoStop_VM_Child** livro de execução para um Determinado VM caso o CPU desça abaixo do limiar configurado para o intervalo de tempo especificado. Este livro tenta então parar o VM.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-subscription"></a>Para direcionar a ação de paragem automática contra todos os VMs numa subscrição

1. Certifique-se de que a variável **External_Stop_ResourceGroupNames** está vazia ou definida para * (wildcard).

2. [Passo opcional] Se desejar excluir alguns VMs da paragem automática, pode adicionar uma lista separada de vírpara os nomes de VM à **variável External_ExcludeVMNames.**

3. Ative o calendário **Schedule_AutoStop_CreateAlert_Parent** a funcionar para criar as regras de alerta métrica stop VM necessárias para todos os VMs na sua subscrição. Executá-lo numa programação permitir-lhe-á criar novas regras de alerta métrico, à medida que os novos VMs são adicionados à subscrição.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-resource-groupmultiple-resource-groups"></a>Para direcionar a ação de paragem automática contra todos os VMs em um grupo de recursos/grupos de recursos múltiplos

1. Adicione uma lista comum separada de nomes de grupos de recursos à **variável External_Stop_ResourceGroupNames.**

2. Opcionalmente, se quiser excluir alguns dos VMs da paragem automática, pode adicionar uma lista separada de vírpara os nomes de VM à **External_ExcludeVMNames** variável.

3. Ative o calendário **Schedule_AutoStop_CreateAlert_Parent** a funcionar para criar as regras de alerta métrica stop **VM** necessárias para todos os VMs dos seus grupos de recursos. Executá-lo em um horário permite-lhe criar novas regras de alerta métrico à medida que novos VMs são adicionados ao(s) grupo de recursos.

### <a name="to-target-the-auto-stop-action-to-a-list-of-vms"></a>Para direcionar a ação de paragem automática para uma lista de VMs

1. Crie uma nova [Agenda](shared-resources/schedules.md#creating-a-schedule) e ligue-a ao **livro de AutoStop_CreateAlert_Parent,** adicionando uma lista separada de nomes VM ao parâmetro **VMList.**

2. Opcionalmente, se quiser excluir alguns VMs da paragem automática, pode adicionar uma lista separada de vírpara os nomes de VM ao **External_ExcludeVMNames** variável.

## <a name="configure-email-notifications"></a>Configurar as notificações por e-mail

Para alterar as notificações de e-mail após a implementação da solução, modifique o grupo de ação que foi criado durante a implementação.  

> [!NOTE]
> As subscrições na Nuvem do Governo Azure não suportam a funcionalidade de e-mail desta solução.

1. No portal Azure, navegue para monitorizar os grupos de ação > Monitor. Selecione o grupo de ação intitulado **StartStop_VM_Notication**.

    ![Página de soluções de gestão de atualização de automatização](media/automation-solution-vm-management/azure-monitor.png)

2. Na página **StartStop_VM_Notification,** clique em **Editar detalhes** em **Detalhes**. Isto abre a página **email/SMS/Push/Voice.** Atualize o endereço de e-mail e clique **em OK** para guardar as suas alterações.

    ![Página de soluções de gestão de atualização de automatização](media/automation-solution-vm-management/change-email.png)

    Alternativamente, você pode adicionar ações adicionais ao grupo de ação, para saber mais sobre grupos de ação, ver [grupos de ação](../azure-monitor/platform/action-groups.md)

Segue-se um e-mail de exemplo que é enviado quando a solução desliga as máquinas virtuais.

![Página de soluções de gestão de atualização de automatização](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>Adicionar/Excluir VMs

A solução fornece a capacidade de adicionar VMs a serem visados pela solução ou especificamente excluir máquinas da solução.

### <a name="add-a-vm"></a>Adicionar uma VM

Existem duas opções que pode utilizar para garantir que um VM está incluído na solução Iniciar/Parar quando funciona.

* Cada um dos [livros-mãe](automation-solution-vm-management.md#runbooks) da solução tem um parâmetro **VMList.** Pode passar uma lista separada de nomes vm separados para este parâmetro ao agendar o livro de execução dos pais apropriado para a sua situação e estes VMs serão incluídos quando a solução funcionar.

* Para selecionar vários VMs, detete o **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames** com os nomes do grupo de recursos que contêm os VMs que pretende iniciar ou parar. Também pode definir este `*`valor para ter a solução executada contra todos os grupos de recursos na subscrição.

### <a name="exclude-a-vm"></a>Excluir uma VM

Para excluir um VM da solução, pode adicioná-lo à **variável External_ExcludeVMNames.** Esta variável é uma lista separada de VMs específicos para excluir da solução Iniciar/Parar. Esta lista está limitada a 140 VMs. Se adicionar mais de 140 VMs a esta lista separada da vírpera, os VMs que estão definidos para serem excluídos podem ser inadvertidamente iniciados ou parados.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Modificar os horários de arranque e encerramento

Gerir os horários de arranque e encerramento nesta solução segue os mesmos passos que delineados no Agendamento de um livro de [execução em Automação Azure.](automation-schedules.md) Tem de haver um horário separado para começar e parar os VMs.

É suportado configurar a solução para parar os VMs num determinado momento. Neste cenário basta criar **um** cronograma e nenhum **início** correspondente agendado. Para efetuar este procedimento, tem de:

1. Certifique-se de que adicionou os grupos de recursos para que os VMs desligassem na **variável External_Stop_ResourceGroupNames.**

2. Crie o seu próprio horário para o tempo que pretende desligar os VMs.

3. Navegue para o **livro de ScheduledStartStop_Parent** e clique em **Agendar**. Isto permite-lhe selecionar o horário que criou no passo anterior.

4. **Selecione Parâmetros e executar definições** e defina o parâmetro **ACTION** to **Stop**.

5. Selecione **OK** para guardar as alterações.

## <a name="next-steps"></a>Passos seguintes

* Para aprender a resolver os Problemas Start/Stop VMs durante as horas de folga, consulte [Os VMs Start/Stop](troubleshoot/start-stop-vm.md)de Resolução de Problemas .

* [Reveja](automation-solution-vm-management-logs.md) os registos de Automação escritos para Registos do Monitor Azure e as consultas de pesquisa de registo de exemplo para analisar o estado dos trabalhos de automação de executantes de Start/Stop VMs.
