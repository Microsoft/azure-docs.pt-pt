---
title: VMs de arranque/paragem durante a solução off-hours
description: Esta solução de gestão VM inicia e para as suas máquinas virtuais Azure numa programação e monitoriza proativamente a partir de registos do Monitor Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 968e609772e08814a9943734d30c16bf6f5972e8
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604717"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>VMs de arranque/paragem durante solução off-hours na Automação Azure

Os **VMs de arranque/paragem durante a solução off-hours** iniciam ou param as suas máquinas virtuais Azure. Inicia ou para máquinas em horários definidos pelo utilizador, fornece insights através de registos do Monitor Do Azure e envia e-mails opcionais utilizando [grupos](../azure-monitor/platform/action-groups.md)de ação . A solução suporta tanto o Gestor de Recursos Azure como os VMs clássicos para a maioria dos cenários. 

Esta solução fornece uma opção de automação descentralizada de baixo custo para os utilizadores que pretendam otimizar os seus custos vm. Com esta solução, pode:

- [Agende VMs para começar e parar.](automation-solution-vm-management-config.md#schedule)
- Agendar VMs para iniciar e parar em ordem ascendente [usando Tags Azure](automation-solution-vm-management-config.md#tags) (não suportados para VMs clássicos).
- VMs de paragem automática com base na [baixa utilização do CPU](automation-solution-vm-management-config.md#cpuutil).

> [!NOTE]
> Os **VMs Start/stop durante a** solução off-hours foram atualizados para suportar as versões mais recentes dos módulos Azure que estão disponíveis.

Seguem-se as limitações com a solução atual:

- Gere VMs em qualquer região, mas só pode ser usado na mesma subscrição que a sua conta Azure Automation.
- Está disponível no Governo Azure e Azure para qualquer região que apoie um espaço de trabalho log Analytics, uma conta Azure Automation, e alertas. As regiões do Governo azure atualmente não suportam a funcionalidade de e-mail.

## <a name="solution-prerequisites"></a>Pré-requisitos de solução

Os livros de execução desta solução funcionam com uma [conta Azure Run As](automation-create-runas-account.md). A conta Executar Como é o método de autenticação preferido porque utiliza a autenticação do certificado em vez de uma palavra-passe que pode expirar ou alterar frequentemente.

Recomendamos que utilize uma conta automatizada separada para os **VMs iniciar/parar durante a solução off-hours.** As versões do módulo Azure são frequentemente atualizadas e os seus parâmetros podem mudar. A solução não é atualizada na mesma cadência e pode não funcionar com versões mais recentes dos cmdlets que utiliza. É aconselhável testar atualizações de módulos numa conta de automatização de teste antes de as importar na sua conta de Automação de Produção.

## <a name="solution-permissions"></a>Permissões de solução

Deve ter determinadas permissões para implantar os **VMs de arranque/paragem durante** a solução fora de horas. As permissões são diferentes se a solução utilizar uma conta de Automação pré-criada e o espaço de trabalho log Analytics a partir das permissões necessárias se a solução criar uma nova conta e espaço de trabalho durante a implementação. 

Não precisa de configurar permissões se for colaborador na subscrição e administrador global no seu inquilino de Diretório Ativo Azure. Se não tiver esses direitos ou precisar de configurar um papel personalizado, certifique-se de que tem as permissões descritas abaixo.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Permissões para conta de Automação pré-existente e espaço de trabalho Log Analytics

Para implementar os **VMs Start/stop durante a** solução off-hours para uma conta de Automação existente e espaço de trabalho Log Analytics, o utilizador que implementa a solução requer as seguintes permissões no âmbito do Grupo de Recursos. Para saber mais sobre papéis, consulte [papéis personalizados para os recursos do Azure.](../role-based-access-control/custom-roles.md)

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

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Permissões para nova conta automation e novo espaço de trabalho log Analytics

Pode implementar os **VMs Start/stop durante** a solução off-hours para uma nova conta De automação e espaço de trabalho log Analytics. Neste caso, o utilizador que implementa a solução necessita das permissões definidas na secção anterior, bem como das permissões definidas nesta secção. 

O utilizador que implementa a solução necessita das seguintes funções:

- Coadministrador na subscrição. Este papel é necessário para criar a Corrida Clássica Como conta se você vai gerir VMs clássicos. [Corrida clássica Como as contas](automation-create-standalone-account.md#create-a-classic-run-as-account) já não são criadas por padrão.
- Aderir ao papel de Desenvolvedor de Aplicações de [Diretório Ativo azure.](../active-directory/users-groups-roles/directory-assign-admin-roles.md) Para obter mais informações sobre a configuração de Executar Como Contas, consulte [Permissões para configurar Executar Como contas](manage-runas-account.md#permissions).
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

Os **VMs de início/paragem durante a solução off-hours** incluem livros de execução pré-configurados, horários e integração com registos do Monitor Azure. Pode utilizar estes elementos para adaptar o arranque e o encerramento dos seus VMs de acordo com as necessidades do seu negócio.

### <a name="runbooks"></a>Runbooks

A tabela seguinte lista os livros de execução que a solução implementa na sua conta Automation. NÃO efaça alterações no código do livro de execução. Em vez disso, escreva o seu próprio livro para novas funcionalidades.

> [!IMPORTANT]
> Não faça nenhum livro de corridas com **criança** anexada ao seu nome.

Todos os livros `WhatIf` dos pais incluem o parâmetro. Quando definido para True, o parâmetro suporta detalhar o comportamento exato que o livro de corridas toma quando executado sem o parâmetro e valida que os VMs corretos são direcionados. Um livro de execução só `WhatIf` executa as suas ações definidas quando o parâmetro está definido para Falso.

|Runbook | Parâmetros | Descrição|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> Ação de Alerta <br> WebHookURI | Ligou do livro dos pais. Este livro de execução cria alertas por recurso para o cenário de paragem automática.|
|AutoStop_CreateAlert_Parent | VMList<br> O que if: Verdadeiro ou Falso  | Cria ou atualiza as regras de alerta azure sobre VMs nos grupos de subscrição ou recursos direcionados. <br> `VMList`é uma lista separada de VMs. Por exemplo, `vm1, vm2, vm3`.<br> `WhatIf`permite a validação da lógica do livro de rcads sem executar.|
|AutoStop_Disable | Nenhuma | Desativa alertas de paragem automática e horário predefinido.|
|AutoStop_VM_Child | WebHookData | Ligou do livro dos pais. As regras de alerta chamam a este livro de corridas para parar um VM clássico.|
|AutoStop_VM_Child_ARM | WebHookData |Ligou do livro dos pais. As regras de alerta chamam este livro de corridas para parar um VM.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Ação: Iniciar ou Parar<br> VMList  | Executa o início ou paragem de ação no grupo VM clássico pela Cloud Services. |
|ScheduledStartStop_Child | VMName <br> Ação: Iniciar ou Parar <br> ResourceGroupName | Ligou do livro dos pais. Executa uma ação de início ou paragem para a paragem programada.|
|ScheduledStartStop_Child_Classic | VMName<br> Ação: Iniciar ou Parar<br> ResourceGroupName | Ligou do livro dos pais. Executa uma ação de início ou paragem para a paragem programada para VMs clássicos. |
|ScheduledStartStop_Parent | Ação: Iniciar ou Parar <br>VMList <br> O que if: Verdadeiro ou Falso | Inicia ou para todos os VMs na subscrição. Editar as `External_Start_ResourceGroupNames` variáveis e `External_Stop_ResourceGroupNames` executar apenas nestes grupos de recursos direcionados. Também pode excluir VMs específicos atualizando a `External_ExcludeVMNames` variável.|
|SequencedStartStop_Parent | Ação: Iniciar ou Parar <br> O que if: Verdadeiro ou Falso<br>VMList| Cria tags nomeadas **sequencestart** e **sequencestop** em cada VM para a qual pretende sequenciar a atividade de arranque/paragem. Estes nomes são sensíveis aos casos. O valor da etiqueta deve ser um inteiro positivo (1, 2, 3) que corresponda à ordem em que pretende iniciar ou parar. <br>**Nota:** Os VMs devem estar `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames`dentro `External_ExcludeVMNames` de grupos de recursos definidos em, e variáveis. Devem ter as etiquetas adequadas para que as ações possam produzir efeitos.|

### <a name="variables"></a>Variáveis

A tabela seguinte lista as variáveis criadas na sua conta De automação. Só modificar variáveis `External`pré-fixas com . Modificar variáveis pré-fixas com `Internal` efeitos indesejáveis.

> [!NOTE]
> As limitações ao nome vm e ao grupo de recursos são em grande parte resultado do tamanho variável.

|Variável | Descrição|
|---------|------------|
|External_AutoStop_Condition | O operador condicional necessário para configurar a condição antes de desencadear um alerta. Os valores `LessThan`aceitáveis `LessThanOrEqual`são, `GreaterThan` `GreaterThanOrEqual`e .|
|External_AutoStop_Description | O alerta para parar o VM se a percentagem de CPU exceder o limiar.|
|External_AutoStop_Frequency | A frequência de avaliação por regra. Este parâmetro aceita a entrada em formato timepan. Os valores possíveis são de 5 minutos a 6 horas. |
|External_AutoStop_MetricName | O nome da métrica de desempenho para a qual a regra de Alerta Azure deve ser configurado.|
|External_AutoStop_Severity | Gravidade do alerta métrico, que pode variar de 0 a 4. |
|External_AutoStop_Threshold | O limiar da regra de alerta azure especificado na variável `External_AutoStop_MetricName`. Os valores percentuais variam entre 1 e 100.|
|External_AutoStop_TimeAggregationOperator | O operador de agregação de tempo aplicado ao tamanho da janela selecionado para avaliar a circunstância. Os valores `Minimum` `Maximum`aceitáveis `Last`são, `Average`e `Total`.|
|External_AutoStop_TimeWindow | O tamanho da janela durante a qual Azure analisa métricas selecionadas para desencadear um alerta. Este parâmetro aceita a entrada em formato timepan. Os valores possíveis são de 5 minutos a 6 horas.|
|External_EnableClassicVMs| Valor especificando se os VMclássicos são direcionados pela solução. O valor padrão é verdadeiro. Detete esta variável para subscrições de Falso solução de nuvem (CSP). VMs clássicos requerem uma [corrida clássica Como conta](automation-create-standalone-account.md#create-a-classic-run-as-account).|
|External_ExcludeVMNames | Lista separada da vírposta de nomes VM para excluir, limitada a 140 VMs. Se adicionar mais de 140 VMs à lista, os VMs que estão definidos para serem excluídos podem ser inadvertidamente iniciados ou parados.|
|External_Start_ResourceGroupNames | Lista separada da vírlém de um ou mais grupos de recursos que são direcionados para as ações de início.|
|External_Stop_ResourceGroupNames | Lista separada da vírlém de um ou mais grupos de recursos que são direcionados para ações de paragem.|
|External_WaitTimeForVMRetrySeconds |O tempo de espera em segundos para que as ações sejam realizadas nos VMs para o **SequencedStartStop_Parent** livro de corridas. Esta variável permite que o livro de execução aguarde as operações infantis por um número determinado de segundos antes de prosseguir com a próxima ação. O tempo máximo de espera é de 10800, ou três horas. O valor padrão é de 2100 segundos.|
|Internal_AutomationAccountName | Especifica o nome da conta Automation.|
|Internal_AutoSnooze_ARM_WebhookURI | O webhook URI apelou para o cenário De Paragem Automática para VMs.|
|Internal_AutoSnooze_WebhookUri | O webhook URI apelou para o cenário AutoStop para VMs clássicos.|
|Internal_AzureSubscriptionId | O ID de assinatura Azure.|
|Internal_ResourceGroupName | O nome do grupo de recursos da conta Automation.|

>[!NOTE]
>Para a `External_WaitTimeForVMRetryInSeconds`variável, o valor predefinido foi atualizado de 600 para 2100. 

Em todos os cenários, `External_Stop_ResourceGroupNames`as `External_ExcludeVMNames` variáveis `External_Start_ResourceGroupNames`, e são necessárias para direcionar os VMs, exceto para as listas de VM separadas da vírlém para o **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**e **ScheduledStartStop_Parent** livros de execução. Ou seja, os seus VMs devem pertencer a grupos de recursos-alvo para que as ações de início e stop ocorram. A lógica funciona semelhante à política do Azure, na medida em que pode visar a subscrição ou grupo de recursos e ter ações herdadas por VMs recém-criados. Esta abordagem evita ter de manter um horário separado para cada VM e gerir partidas e paragens em escala.

### <a name="schedules"></a>Agendas

A tabela seguinte lista cada um dos horários predefinidos criados na sua conta Automation.Pode modificá-los ou criar os seus próprios horários personalizados.Por padrão, todos os horários são desativados, exceto para os horários **de Scheduled_StartVM** e **Scheduled_StopVM.**

Não permita todos os horários, porque fazê-lo pode criar ações de horário sucintas. É melhor determinar quais as otimizações que pretende fazer e modificá-las em conformidade. Consulte os cenários de exemplo na secção de visão geral para obter mais explicações.

|Nome da agenda | Frequência | Descrição|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | A cada 8 horas | Executa o **AutoStop_CreateAlert_Parent** livro de corridas a cada 8 horas, o que por sua vez para os valores baseados em VM em `External_Start_ResourceGroupNames`, `External_Stop_ResourceGroupNames`e `External_ExcludeVMNames` variáveis. Em alternativa, pode especificar uma lista separada de VMs `VMList` com a utilização do parâmetro.|
|Scheduled_StopVM | Definido pelo utilizador, diariamente | Executa o **ScheduledStopStart_Parent** livro de `Stop` corridas com um parâmetro de todos os dias no momento especificado.Para automaticamente todos os VMs que cumprem as regras definidas por ativos variáveis.Ativar o **programado-startVM**relacionado .|
|Scheduled_StartVM | Definido pelo utilizador, diariamente | Executa o **livro de ScheduledStopStart_Parent** `Start` com um valor de parâmetro de cada dia no momento especificado. Inicia automaticamente todos os VMs que cumprem as regras definidas por ativos variáveis.Ativar o **programado-stopVM**relacionado .|
|Paragem sequenciada | 1:00 AM (UTC), todas as sextas-feiras | Executa o livro de Sequenced_Parent `Stop` com um valor de parâmetro de cada sexta-feira no momento especificado.Sequencialmente (ascendente) para todos os VMs com uma etiqueta de **SequenceStop** definida pelas variáveis apropriadas. Para obter mais informações sobre valores de etiqueta e variáveis de ativos, consulte a secção Runbooks.Ativar o horário relacionado, **Sequenciado-StartVM**.|
|Iniciado-Arranque-VM | 13:00 (UTC), todas as segundas-feiras | Executa o **livro de SequencedStopStart_Parent** `Start` com um valor de parâmetro de cada segunda-feira no momento especificado. Sequencialmente (descendente) inicia todos os VMs com uma etiqueta de **SequenceStart** definida pelas variáveis apropriadas. Para obter mais informações sobre valores de etiquetae ativos variáveis, consulte os [Livros de Execução.](#runbooks) Ativar o horário relacionado, **Sequenced-StopVM**.

## <a name="use-of-the-solution-with-classic-vms"></a>Utilização da solução com VMs clássicos

Se estiver a utilizar os **VMs Start/stop durante** a solução off-hours para VMs clássicos, então a Automação processa todos os seus VMs sequencialmente por serviço de nuvem. Os VMs ainda são processados em paralelo através de diferentes serviços na nuvem. 

Para utilizar a solução com VMs clássicos, precisa de uma conta Classic Run As, que não é criada por defeito. Para obter instruções sobre a criação de uma conta Classic Run As, consulte [Create a Classic Run As account](automation-create-standalone-account.md#create-a-classic-run-as-account).

Se tem mais de 20 VMs por serviço na nuvem, aqui ficam algumas recomendações:

* Crie vários horários com o livro de execução dos pais **ScheduledStartStop_Parent** e especificando 20 VMs por programação. 
* Nas propriedades de horário, utilize o parâmetro para especificar os `VMList` nomes vm como uma lista separada da vírvia. 

Caso contrário, se o trabalho de Automação para esta solução dura mais de três horas, é temporariamente descarregado ou parado de acordo com o limite [de ações justas.](automation-runbook-execution.md#fair-share)

As subscrições do Azure CSP suportam apenas o modelo Do Gestor de Recursos Azure. Os serviços de Gestor de Recursos Não-Azure não estão disponíveis no programa. Quando os **VMs Iniciar/paragem durante as soluçãos fora de horas** se executam, poderá receber erros, uma vez que tem cmdlets para gerir recursos clássicos. Para saber mais sobre a CSP, consulte [os serviços disponíveis nas subscrições do CSP.](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services) Se utilizar uma subscrição de CSP, deverá definir a [variável External_EnableClassicVMs](#variables) para False após a implementação.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-solution"></a>Ativar a solução

Para começar a utilizar a solução, siga os passos na [solução Enable Start/stop VMs](automation-solution-vm-management-enable.md).

## <a name="view-the-solution"></a>Ver a solução

Utilize um dos seguintes mecanismos para aceder à solução depois de a ter ativado:

* Na sua conta de Automação, selecione **Start/Stop VM** em **Recursos Relacionados**. Na página 'Iniciar/Parar VM', selecione **Gerir a solução** do lado direito da página, em **soluções 'Gerir Start/Stop VM '.**

* Navegue para o espaço de trabalho log Analytics ligado à sua conta Deautomação. Depois de selecionar o espaço de trabalho, escolha **Soluções** do painel esquerdo. Na página Soluções, selecione a solução **Start-Stop-VM [workspace]** da lista.  

A seleção da solução mostra a página de solução **Start-Stop-VM[workspace].** Aqui pode rever detalhes importantes, como as informações no azulejo **StartStopVM.** Tal como no seu espaço de trabalho log analytics, este azulejo apresenta uma contagem e uma representação gráfica dos trabalhos do livro de corridas para a solução que começou e terminou com sucesso.

![Página de soluções de gestão de atualização de automatização](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Pode efetuar uma análise mais aprofundada dos registos de trabalho clicando no azulejo dodo. O dashboard de solução mostra histórico de trabalho e consultas de pesquisa de registo predefinidas. Mude para o portal avançado de análise de registos para pesquisar com base nas suas consultas de pesquisa.

## <a name="update-the-solution"></a>Update the solution (Atualizar a solução)

Se implementou uma versão anterior desta solução, apague-a da sua conta antes de implementar uma versão atualizada. Siga os passos para [remover a solução](#remove-the-solution) e siga os passos para [implementar a solução](automation-solution-vm-management-enable.md).

## <a name="remove-the-solution"></a>Remove the solution (Remover a solução)

Se já não precisar de utilizar a solução, pode eliminá-la da conta Automation. A eliminação da solução apenas remove os livros de execução. Não apaga os horários ou variáveis que foram criados quando a solução foi adicionada. Remova estes ativos manualmente se não estiver a usá-los com outros livros de execução.

Para eliminar a solução:

1. Na sua conta de Automação, selecione **Linked workspace** sob **recursos relacionados.**

2. Selecione **Ir para o espaço de trabalho**.

3. Clique em **Soluções** em **Geral**. 

4. Na página Soluções, selecione a solução **Start-Stop-VM[Workspace]**. 

5. Na página **VMManagementSolution[Workspace],** selecione **Eliminar** a partir do menu.<br><br> ![Eliminar solução VM Mgmt](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. Na janela **Eliminar Solução,** confirme se pretende eliminar a solução.

7. Enquanto a informação é verificada e a solução é eliminada, pode acompanhar o progresso no âmbito das Notificações , **escolhidas**a partir do menu. É devolvido à página Soluções após o início do processo para remover a solução.

A conta de Automação e o espaço de trabalho log Analytics não são eliminados como parte deste processo. Se não quiser manter o espaço de trabalho do Log Analytics, deve eliminá-lo manualmente do portal Azure:

1. Procure e selecione espaços de **trabalho Log Analytics**.

2. Na página do espaço de trabalho Log Analytics, selecione o espaço de trabalho.

3. **Selecione Eliminar** a partir do menu na página de definições do espaço de trabalho.

4. Se não quiser manter os componentes da [solução](#solution-components)de conta Azure Automation, pode eliminar manualmente cada um deles.

## <a name="next-steps"></a>Passos seguintes

[Ative](automation-solution-vm-management-enable.md) os **VMs de arranque/paragem durante a** solução off-hours para os seus VMs Azure.
