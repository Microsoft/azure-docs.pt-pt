---
title: VMs de arranque/paragem durante a solução off-hours
description: Esta solução de gestão VM inicia e para as suas máquinas virtuais Azure numa programação e monitoriza proativamente a partir de registos do Monitor Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: cef3176c99cd57ae229b602feb3c825081fcfe3e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548379"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Solução Iniciar/Parar VMs fora do horário de expediente na Automatização do Azure

Os VMs start/stop durante o início da solução off-hours e para as suas máquinas virtuais Azure em horários definidos pelo utilizador, fornece informações através de registos do Monitor Azure e envia e-mails opcionais utilizando [grupos](../azure-monitor/platform/action-groups.md)de ação . Suporta tanto o Gestor de Recursos Azure como os VMs clássicos para a maioria dos cenários. 

Para utilizar esta solução com VMs clássicos, precisa de uma conta Classic RunAs, que não é criada por defeito. Para obter instruções sobre a criação de uma conta RunAs [Clássicas, consulte contas Clássicas Run-As](automation-create-standalone-account.md#classic-run-as-accounts).

> [!NOTE]
> Os VMs Start/Stop durante a solução off-hours foram atualizados para suportar as versões mais recentes dos módulos Azure que estão disponíveis.

Esta solução fornece uma opção de automação descentralizada de baixo custo para os utilizadores que pretendam otimizar os seus custos vm. Com esta solução, pode:

- [Agende VMs para começar e parar.](automation-solution-vm-management-config.md#schedule)
- Agendar VMs para iniciar e parar em ordem ascendente [usando Tags Azure](automation-solution-vm-management-config.md#tags) (não suportados para VMs clássicos).
- VMs de paragem automática com base na [baixa utilização do CPU](automation-solution-vm-management-config.md#cpuutil).

Seguem-se as limitações com a solução atual:

- Esta solução gere VMs em qualquer região, mas só pode ser usada na mesma subscrição que a sua conta Azure Automation.
- Esta solução está disponível no Governo Azure e Azure para qualquer região que suporte um espaço de trabalho log Analytics, uma conta Azure Automation e Alerts. As regiões do Governo azure não suportam atualmente a funcionalidade de e-mail.

> [!NOTE]
> Se estiver a usar a solução para VMs clássicos, então todos os seus VMs serão processados sequencialmente por serviço de nuvem. As máquinas virtuais ainda são processadas paralelamente através de diferentes serviços de nuvem. Se tiver mais de 20 VMs por serviço na nuvem, recomendamos a criação de vários horários com o livro de executantes dos pais **ScheduledStartStop_Parent** e especificar 20 VMs por horário. Nas propriedades de horário, especificar como uma lista separada de vírinas, nomes VM no parâmetro **VMList.** Caso contrário, se o trabalho de Automação para esta solução demorar mais de três horas, é temporariamente descarregado ou parado de acordo com o limite [de ações justas.](automation-runbook-execution.md#fair-share)
>
> As subscrições do Azure Cloud Solution Provider (Azure CSP) suportam apenas o modelo Do Gestor de Recursos Azure, não estão disponíveis serviços de Gestor de Recursos não Azure no programa. Quando a solução Iniciar/Parar funciona poderá receber erros, uma vez que tem cmdlets para gerir recursos clássicos. Para saber mais sobre a CSP, consulte [os serviços disponíveis nas subscrições do CSP.](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services) Se utilizar uma subscrição de CSP, deverá modificar a [**variável External_EnableClassicVMs**](#variables) para **Falsa** após a implementação.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Os livros de execução desta solução funcionam com uma [conta Azure Run As](automation-create-runas-account.md). O Executar Como conta é o método de autenticação preferido, pois utiliza autenticação de certificado em vez de uma palavra-passe que pode expirar ou alterar frequentemente.

Recomendamos que utilize uma Conta de Automação separada para a solução Start/Stop VM. Isto porque as versões do módulo Azure são frequentemente atualizadas e os seus parâmetros podem mudar. A solução Start/Stop VM não é atualizada na mesma cadência, pelo que pode não funcionar com versões mais recentes dos cmdlets que utiliza. Recomendamos também que teste as atualizações do módulo numa Conta de Automatização de Teste antes de as importar na sua Conta de Automação de Produção.

### <a name="permissions"></a>Permissões

Existem certas permissões que um utilizador deve ter de implementar os VMs Start/Stop durante a solução de horas de folga. Estas permissões são diferentes se utilizar uma conta de automação pré-criada e log analytics espaço de trabalho ou criar novas durante a implementação. Se é colaborador na subscrição e administrador global no seu inquilino do Diretório Ativo Azure, não precisa de configurar as seguintes permissões. Se não tiver esses direitos ou precisar de configurar uma função personalizada, consulte as permissões exigidas abaixo.

#### <a name="pre-existing-automation-account-and-log-analytics-workspace"></a>Conta de Automação pré-existente e espaço de trabalho log Analytics

Para implementar os VMs de início/paragem durante o horário de folga para uma conta de automação existente e espaço de trabalho log Analytics, o utilizador que implementa a solução requer as seguintes permissões no **Grupo de Recursos**. Para saber mais sobre papéis, consulte [papéis personalizados para os recursos do Azure.](../role-based-access-control/custom-roles.md)

| Permissão | Âmbito|
| --- | --- |
| Microsoft.Automation/automationAccounts/read | Grupo de Recursos |
| Microsoft.Automação/automatizaçãoContas/variáveis/escrita | Grupo de Recursos |
| Microsoft.Automação/automatizaçãoContas/horários/escrita | Grupo de Recursos |
| Microsoft.Automação/automatizaçãoContas/livros de execução/escrita | Grupo de Recursos |
| Microsoft.Automação/automatizaçãoContas/ligações/escrita | Grupo de Recursos |
| Microsoft.Automação/automatizaçãoContas/certificados/escrita | Grupo de Recursos |
| Microsoft.Automação/automatizaçãoContas/módulos/escrita | Grupo de Recursos |
| Microsoft.Automação/automatizaçãoContas/módulos/leitura | Grupo de Recursos |
| Microsoft.automation/automationAccounts/jobSchedules/write | Grupo de Recursos |
| Microsoft.Automação/automatizaçãoContas/empregos/escrita | Grupo de Recursos |
| Microsoft.Automação/automatizaçãoContas/empregos/leitura | Grupo de Recursos |
| Microsoft.OperationsManagement/solutions/write | Grupo de Recursos |
| Microsoft.OperationalInsights/workspaces/* | Grupo de Recursos |
| Microsoft.Insights/diagnósticoDefinições/escrita | Grupo de Recursos |
| Microsoft.Insights/ActionGroups/Write | Grupo de Recursos |
| Microsoft.Insights/ActionGroups/read | Grupo de Recursos |
| Microsoft.Recursos/subscrições/recursosGroups/read | Grupo de Recursos |
| Microsoft.Recursos/implementações/* | Grupo de Recursos |

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>Nova Conta de Automação e um novo espaço de trabalho log Analytics

Para implementar os VMs de início/paragem durante o horário de folga para uma nova Conta de Automação e espaço de trabalho log Analytics, o utilizador que implementa a solução necessita das permissões definidas na secção anterior, bem como as seguintes permissões:

- Coadministrador na subscrição - Só é necessário criar o Classic Run As Account se você vai gerir VMs clássicos. [As Contas RunAs Clássicas](automation-create-standalone-account.md#classic-run-as-accounts) já não são criadas por padrão.
- Membro do papel de Desenvolvedor de **Aplicações** de [Diretório Ativo Azure.](../active-directory/users-groups-roles/directory-assign-admin-roles.md) Para obter mais informações sobre a configuração de Executar Como Contas, consulte [Permissões para configurar Executar Como contas](manage-runas-account.md#permissions).
- Colaborador na subscrição ou nas seguintes permissões.

| Permissão |Âmbito|
| --- | --- |
| Microsoft.Authorization/Operations/read | Subscrição|
| Microsoft.Autorizações/permissões/leitura |Subscrição|
| Microsoft.Autorizações/roleAssignments/read | Subscrição |
| Microsoft.Authorization/roleAssignments/write | Subscrição |
| Microsoft.Autorizações/tarefas/exclusão | Subscrição |
| Microsoft.Automação/automatizaçãoContas/ligações/leitura | Grupo de Recursos |
| Microsoft.Automação/automatizaçãoContas/certificados/leitura | Grupo de Recursos |
| Microsoft.Automation/automationAccounts/write | Grupo de Recursos |
| Microsoft.OperationalInsights/workspaces/write | Grupo de Recursos |

## <a name="solution-components"></a>Componentes da solução

Esta solução inclui livros de execução, horários e integração pré-configurados com registos do Monitor Azure para que possa adaptar o arranque e o encerramento das suas máquinas virtuais de acordo com as suas necessidades de negócio.

### <a name="runbooks"></a>Runbooks

A tabela seguinte lista os livros de execução implantados na sua conta Automation por esta solução. Não faça alterações no código do livro de execução. Em vez disso, escreva o seu próprio livro para novas funcionalidades.

> [!IMPORTANT]
> Não faça qualquer livro de execução diretamente com *criança* anexada ao seu nome.

Todos os livros de execução dos pais incluem o parâmetro _WhatIf._ Quando definido para **True**, _o WhatIf_ suporta detalhar o comportamento exato que o livro de corridas toma quando executado sem o parâmetro _WhatIf_ e valida os VMs corretos estão a ser alvo. Um livro de execução só executa as suas ações definidas quando o parâmetro _WhatIf_ está definido para **Falso**.

|Runbook | Parâmetros | Descrição|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> Ação de Alerta <br> WebHookURI | Ligou do livro dos pais. Este livro de execução cria alertas numa base por recurso para o cenário AutoStop.|
|AutoStop_CreateAlert_Parent | VMList<br> O que if: Verdadeiro ou Falso  | Cria ou atualiza as regras de alerta azure sobre VMs nos grupos de subscrição ou recursos direcionados. <br> VMList: Lista separada da Víramida de VMs. Por exemplo, _vm1, vm2, vm3_.<br> *O que If* valida a lógica do livro sem executar.|
|AutoStop_Disable | nenhuma | Desativa os alertas AutoStop e o horário predefinido.|
|AutoStop_VM_Child | WebHookData | Ligou do livro dos pais. As regras de alerta chamam a este livro de corridas para parar o Clássico VM.|
|AutoStop_VM_Child_ARM | WebHookData |Ligou do livro dos pais. As regras de alerta chamam este livro de corridas para parar o VM.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Ação: Iniciar ou Parar<br> VMList  | Este livro de corridas usado para realizar o início ou paragem de ação no grupo VM clássico pela Cloud Services.<br> VMList: Lista separada da Víramida de VMs. Por exemplo, _vm1, vm2, vm3_. |
|ScheduledStartStop_Child | VMName <br> Ação: Iniciar ou Parar <br> ResourceGroupName | Ligou do livro dos pais. Executa uma ação de início ou paragem para a paragem programada.|
|ScheduledStartStop_Child_Classic | VMName<br> Ação: Iniciar ou Parar<br> ResourceGroupName | Ligou do livro dos pais. Executa uma ação de início ou paragem para a paragem programada para VMs clássicos. |
|ScheduledStartStop_Parent | Ação: Iniciar ou Parar <br>VMList <br> O que if: Verdadeiro ou Falso | Esta definição afeta todos os VMs na subscrição. Editar o **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames** apenas executar nestes grupos de recursos direcionados. Também pode excluir VMs específicos atualizando a **variável External_ExcludeVMNames.**<br> VMList: Lista separada da Víramida de VMs. Por exemplo, _vm1, vm2, vm3_.<br> _O que If_ valida a lógica do livro sem executar.|
|SequencedStartStop_Parent | Ação: Iniciar ou Parar <br> O que if: Verdadeiro ou Falso<br>VMList| Crie tags nomeadas **sequencestart** e **sequencestop** em cada VM para a qual pretende sequenciar a atividade de arranque/paragem. Estes nomes são sensíveis aos casos. O valor da etiqueta deve ser um inteiro positivo (1, 2, 3) que corresponda à ordem em que pretende iniciar ou parar. <br> VMList: Lista separada da Víramida de VMs. Por exemplo, _vm1, vm2, vm3_. <br> _O que If_ valida a lógica do livro sem executar. <br> **Nota:** Os VMs devem estar dentro de grupos de recursos definidos como External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames e External_ExcludeVMNames nas variáveis Azure Automation. Devem ter as etiquetas adequadas para que as ações possam produzir efeitos.|

### <a name="variables"></a>Variáveis

A tabela seguinte lista as variáveis criadas na sua conta De automação. Só modificar variáveis pré-fixas com **Externo**. Modificar variáveis pré-fixas com **efeitos internos** causa efeitos indesejáveis.

|Variável | Descrição|
|---------|------------|
|External_AutoStop_Condition | O operador condicional necessário para configurar a condição antes de desencadear um alerta. Os valores aceitáveis são **GreaterThan**, **GreaterThanEqual**, **LessThan**, e **LessThanOrEqual**.|
|External_AutoStop_Description | O alerta para parar o VM se a percentagem de CPU exceder o limiar.|
|External_AutoStop_Frequency | A frequência de avaliação por regra. Este parâmetro aceita a entrada em formato timepan. Os valores possíveis são de 5 minutos a 6 horas. |
|External_AutoStop_MetricName | O nome da métrica de desempenho para a qual a regra de Alerta Azure deve ser configurado.|
|External_AutoStop_Severity | Gravidade do alerta métrico, que pode variar de 0 a 4. |
|External_AutoStop_Threshold | O limiar da regra de alerta Azure especificado na _variável External_AutoStop_MetricName_. Os valores percentuais podem variar de 1 a 100.|
|External_AutoStop_TimeAggregationOperator | O operador de agregação de tempo, que é aplicado ao tamanho da janela selecionado para avaliar a circunstância. Os valores aceitáveis são **Médios,** **Mínimos,** **Máximo,** **Total**e **Último.**|
|External_AutoStop_TimeWindow | O tamanho da janela durante o qual Azure analisa métricas selecionadas para desencadear um alerta. Este parâmetro aceita a entrada em formato timepan. Os valores possíveis são de 5 minutos a 6 horas.|
|External_EnableClassicVMs| Especifica se os VMclássicos são visados pela solução. O valor padrão é verdadeiro. Isto deve ser definido como Falso para assinaturas De CSP. Os VMs clássicos requerem uma [conta clássica run-as](automation-create-standalone-account.md#classic-run-as-accounts).|
|External_ExcludeVMNames | Introduza nomes VM a excluir, separando nomes utilizando uma vírem sem espaços. Isto é limitado a 140 VMs. Se adicionar mais de 140 VMs a esta lista separada da vírpera, os VMs que estão definidos para serem excluídos podem ser inadvertidamente iniciados ou parados.|
|External_Start_ResourceGroupNames | Especifica um ou mais grupos de recursos, separando valores utilizando uma vírvia, direcionado para as ações de início.|
|External_Stop_ResourceGroupNames | Especifica um ou mais grupos de recursos, separando valores através de uma vírvia, direcionado para ações de paragem.|
|External_WaitTimeForVMRetrySeconds |O tempo de espera em segundos para que as ações sejam realizadas nos VMs para o livro de arranque/paragem sequenciado.<br> O valor predefinido é de 2100 segundos e suporta configurar até um valor máximo de 10800 ou três horas.|
|Internal_AutomationAccountName | Especifica o nome da conta Automation.|
|Internal_AutoSnooze_ARM_WebhookURI | Especifica Webhook URI chamado para o cenário De Paragem Automática para VMs clássicos.|
|Internal_AutoSnooze_WebhookUri | Especifica Webhook URI chamado para o cenário DeParagem Automático.|
|Internal_AzureSubscriptionId | Especifica o ID de subscrição do Azure.|
|Internal_ResourceGroupName | Especifica o nome do grupo de recursos da conta Automation.|

>[!NOTE]
>Para a variável **External_WaitTimeForVMRetryInSeconds,** o valor predefinido foi atualizado de 600 para 2100. Esta variável permite que o livro de execução de **cenário sonerado de início/paragem** aguarde as operações da criança durante um número determinado de segundos antes de prosseguir com a próxima ação.
>

Em todos os cenários, as **variáveis External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**e **External_ExcludeVMNames** são necessárias para direcionar as VMs, com exceção de fornecer uma lista separada de VMs para o **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**e **ScheduledStartStop_Parent** livros de execução. Ou seja, os seus VMs devem residir em grupos de recursos-alvo para que as ações de início e stop ocorram. A lógica funciona semelhante à política do Azure, na medida em que pode visar a subscrição ou grupo de recursos e ter ações herdadas por VMs recém-criados. Esta abordagem evita ter de manter um horário separado para cada VM e gerir partidas e paragens em escala.

### <a name="schedules"></a>Agendas

A tabela seguinte lista cada um dos horários predefinidos criados na sua conta Automation.Pode modificá-los ou criar os seus próprios horários personalizados.Por predefinição, todos os horários são desativados, exceto para **Scheduled_StartVM** e **Scheduled_StopVM**.

Não deve permitir todos os horários, porque isso pode criar ações de calendário sobrepostas. É melhor determinar quais as otimizações que pretende realizar e modificar em conformidade. Consulte os cenários de exemplo na secção de visão geral para obter mais explicações.

|Nome da agenda | Frequência | Descrição|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | A cada 8 horas | Executa o livro de AutoStop_CreateAlert_Parent a cada 8 horas, o que por sua vez para os valores baseados em VM em variáveis External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames e External_ExcludeVMNames nas variáveis Azure Automation. Em alternativa, pode especificar uma lista separada de VMs através do parâmetro VMList.|
|Scheduled_StopVM | Utilizador definido, diariamente | Executa o livro de Scheduled_Parent com um parâmetro de _stop_ todos os dias na hora especificada.Para automaticamente todos os VMs que cumprem as regras definidas pelas variáveis de ativos.Ativar o horário relacionado, **Programado-StartVM**.|
|Scheduled_StartVM | Utilizador definido, diariamente | Executa o Scheduled_Parent livro de corridas com um parâmetro de _início_ todos os dias na hora especificada. Inicia automaticamente todos os VMs que cumprem as regras definidas pelas variáveis apropriadas.Ativar o horário relacionado, **Paragem Programada-VM**.|
|Paragem sequenciada | 1:00 AM (UTC), todas as sextas-feiras | Executa o Sequenced_Parent livro de corridas com um parâmetro de _stop_ todas as sextas-feiras no momento especificado.Sequencialmente (ascendente) para todos os VMs com uma etiqueta de **SequenceStop** definida pelas variáveis apropriadas. Para obter mais informações sobre valores de etiqueta e variáveis de ativos, consulte a secção Runbooks.Ativar o horário relacionado, **Sequenciado-StartVM**.|
|Iniciado-Arranque-VM | 13:00 (UTC), todas as segundas-feiras | Executa o livro de Sequenced_Parent com um parâmetro de _início_ todas as segundas-feiras à hora especificada. Sequencialmente (descendente) inicia todos os VMs com uma etiqueta de **SequenceStart** definida pelas variáveis apropriadas. Para obter mais informações sobre valores de etiqueta e variáveis de ativos, consulte a secção Runbooks. Ativar o horário relacionado, **Sequenced-StopVM**.|

## <a name="enable-the-solution"></a>Ativar a solução

Para começar a utilizar a solução, execute os passos na [solução Enable Start/Stop VMs](automation-solution-vm-management-enable.md).

## <a name="viewing-the-solution"></a>Visualizar a solução

Pode aceder à solução depois de a ter ativado a partir de uma das seguintes formas:

* Na sua conta de Automação, selecione **Start/Stop VM** em **Recursos Relacionados**. Na página **'Iniciar/Parar VM',** selecione **Gerir a solução** do lado direito da página, sob a secção **Gerir soluções VM Arranque/Paragem**.

* Navegue para o espaço de trabalho log Analytics ligado à sua conta Automation e, depois de selecionar o espaço de trabalho, selecione **Soluções** a partir do painel da esquerda. Na página **Soluções,** selecione a solução **Start-Stop-VM [workspace]** da lista.  

A seleção da solução mostra a página de solução **Start-Stop-VM[workspace].** Aqui pode rever detalhes importantes, como o azulejo **StartStopVM.** Tal como no seu espaço de trabalho log analytics, este azulejo apresenta uma contagem e uma representação gráfica dos trabalhos do livro de corridas para a solução que começou e terminou com sucesso.

![Página de soluções de gestão de atualização de automatização](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

A partir daqui, pode efetuar uma análise mais aprofundada dos registos de trabalho clicando no azulejo dodo. O dashboard de solução mostra histórico de trabalho e consultas de pesquisa de registo pré-definidas. Mude para o portal avançado de análise de registos para pesquisar com base nas suas consultas de pesquisa.

## <a name="update-the-solution"></a>Update the solution (Atualizar a solução)

Se implementou uma versão anterior desta solução, tem primeiro de a apagar da sua conta antes de implementar uma versão atualizada. Siga os passos para [remover a solução](#remove-the-solution) e siga os passos para [implementar a solução](automation-solution-vm-management-enable.md).

## <a name="remove-the-solution"></a>Remove the solution (Remover a solução)

Se decidir que já não precisa de utilizar a solução, pode eliminá-la da conta Automation. A eliminação da solução apenas remove os livros de execução. Não apaga os horários ou variáveis que foram criados quando a solução foi adicionada. Esses ativos que precisa de apagar manualmente se não estiver a usá-los com outros livros de execução.

Para eliminar a solução, execute os seguintes passos:

1. Na sua conta de Automação, sob **recursos Relacionados,** selecione espaço de **trabalho Linked**.

2. Selecione **Ir para o espaço de trabalho**.

3. Em **Geral,** selecione **Soluções**. 

4. Na página **Soluções,** selecione a solução **Start-Stop-VM[Workspace]**. Na página **VMManagementSolution[Workspace],** a partir do menu, **selecione Delete**.<br><br> ![Eliminar solução VM Mgmt](media/automation-solution-vm-management/vm-management-solution-delete.png)

5. Na janela **Eliminar Solução,** confirme se pretende eliminar a solução.

6. Enquanto a informação é verificada e a solução é eliminada, pode acompanhar o seu progresso no âmbito de **Notificações** do menu. É devolvido à página **Soluções** após o início do processo para remover a solução.

A conta de Automação e o espaço de trabalho log Analytics não são eliminados como parte deste processo. Se não quiser reter o espaço de trabalho do Log Analytics, tem de o eliminar manualmente. Isto pode ser realizado a partir do portal Azure:

1. No portal Azure, procure e selecione espaços de **trabalho Log Analytics**.

2. Na página dos espaços de **trabalho Log Analytics,** selecione o espaço de trabalho.

3. **Selecione Eliminar** a partir do menu na página de definições do espaço de trabalho.

Se não quiser reter os componentes da conta Azure Automation, pode eliminar manualmente cada um deles. Para a lista de livros de execução, variáveis e horários criados pela solução, consulte os componentes da [Solução.](#solution-components)

## <a name="next-steps"></a>Passos seguintes

[Ative](automation-solution-vm-management-enable.md) a solução Start/Stop durante o horário de folga para os seus VMs Azure.
