---
title: VMs de arranque/paragem de automação Azure durante a visão geral fora de horas
description: Este artigo descreve os VMs de início/paragem durante o período de folga, que inicia ou para VMs num horário e os monitoriza proativamente a partir de registos do Monitor Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/28/2020
ms.topic: conceptual
ms.openlocfilehash: e2f23f4045f0326ffea14ddeb4d588261872188f
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743711"
---
# <a name="startstop-vms-during-off-hours-overview"></a>VMs de arranque/paragem durante a visão geral fora de horas

Os VMs de arranque/paragem durante o início ou paragem ativam Os VMs Azure. Inicia ou para máquinas em horários definidos pelo utilizador, fornece insights através de registos do Monitor Do Azure e envia e-mails opcionais utilizando [grupos](../azure-monitor/platform/action-groups.md)de ação . A funcionalidade pode ser ativada tanto no Azure Resource Manager como nos VMs clássicos para a maioria dos cenários. 

Esta função utiliza cmdlet [Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0) para iniciar VMs. Utiliza [stop-AzureRmVM](https://docs.microsoft.com/powershell/module/AzureRM.Compute/Stop-AzureRmVM?view=azurermps-6.13.0) para parar VMs.

> [!NOTE]
> Os VMs start/stop durante o horário de folga foram atualizados para suportar as versões mais recentes dos módulos Azure que estão disponíveis. A versão atualizada desta funcionalidade, disponível no Marketplace, não suporta módulos AzureRM porque emigrámos de módulos AzureRM para Az.

A funcionalidade fornece uma opção de automação de baixo custo descentralizada para os utilizadores que pretendam otimizar os seus custos de VM. Pode utilizar a funcionalidade para:

- [Agende VMs para começar e parar.](automation-solution-vm-management-config.md#schedule)
- Agende VMs para iniciar e parar em ordem ascendente [usando tags Azure](automation-solution-vm-management-config.md#tags). Esta atividade não é suportada para VMs clássicos.
- VMs de paragem automática com base na [baixa utilização do CPU](automation-solution-vm-management-config.md#cpuutil).

Seguem-se limitações com a funcionalidade atual:

- Gere VMs em qualquer região, mas só pode ser usado na mesma subscrição que a sua conta Azure Automation.
- Está disponível no Governo Azure e Azure para qualquer região que apoie um espaço de trabalho log Analytics, uma conta Azure Automation, e alertas. As regiões do Governo azure atualmente não suportam a funcionalidade de e-mail.

## <a name="prerequisites"></a>Pré-requisitos

Os livros de execução para os VMs de início/paragem durante o horário de folga funcionam com uma [conta Azure Run As](automation-create-runas-account.md). A conta Executar Como é o método de autenticação preferido porque utiliza a autenticação do certificado em vez de uma palavra-passe que pode expirar ou alterar frequentemente.

Recomendamos que utilize uma conta automatizada separada para trabalhar com VMs ativados para os VMs de arranque/paragem durante a funcionalidade off-hours. As versões do módulo Azure são frequentemente atualizadas e os seus parâmetros podem mudar. A funcionalidade não é atualizada na mesma cadência e pode não funcionar com versões mais recentes dos cmdlets que utiliza. É aconselhável testar atualizações de módulos numa conta de automatização de teste antes de as importar na sua conta de Automação de Produção.

## <a name="permissions"></a>Permissões

Deve ter certas permissões para ativar os VMs para os VMs de início/paragem durante a função off-hours. As permissões são diferentes dependendo se a funcionalidade utiliza uma conta de Automação pré-criada e um espaço de trabalho log Analytics ou cria uma nova conta e espaço de trabalho. 

Não precisa de configurar permissões se for colaborador na subscrição e administrador global no seu inquilino de Diretório Ativo Azure (AD). Se não tiver esses direitos ou precisar de configurar um papel personalizado, certifique-se de que tem as permissões descritas abaixo.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Permissões para conta de Automação pré-existente e espaço de trabalho Log Analytics

Para ativar os VMs para os VMs de início/paragem durante o off-hours utilizando uma conta de Automação existente e espaço de trabalho log Analytics, precisa das seguintes permissões no âmbito do Grupo de Recursos. Para saber mais sobre papéis, consulte [papéis personalizados para os recursos do Azure.](../role-based-access-control/custom-roles.md)

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

Pode ativar os VMs para os VMs de início/paragem durante a funcionalidade off-hours utilizando uma nova conta De Automação e espaço de trabalho log Analytics. Neste caso, necessita das permissões definidas na secção anterior, bem como das permissões definidas nesta secção. Também requer as seguintes funções:

- Coadministrador na subscrição. Este papel é necessário para criar a Corrida Clássica Como conta se você vai gerir VMs clássicos. [Corrida clássica Como as contas](automation-create-standalone-account.md#create-a-classic-run-as-account) já não são criadas por padrão.
- Aderir ao papel de Desenvolvedor de Aplicações [AD Azure.](../active-directory/users-groups-roles/directory-assign-admin-roles.md) Para obter mais informações sobre a configuração de Executar Como Contas, consulte [Permissões para configurar Executar Como contas](manage-runas-account.md#permissions).
- Colaborador na subscrição ou nas seguintes permissões.

| Permissão |Âmbito|
| --- | --- |
| Microsoft.Authorization/Operations/read | Subscrição|
| Microsoft.Autorizações/permissões/leitura |Subscrição|
| Microsoft.Autorizações/roleAssignments/read | Subscrição |
| Microsoft.Authorization/roleAssignments/write | Subscrição |
| Microsoft.Autorizações/tarefas/exclusão | Subscrição || Microsoft.Automação/automatizaçãoContas/ligações/leitura | Grupo de Recursos |
| Microsoft.Automação/automatizaçãoContas/certificados/leitura | Grupo de Recursos |
| Microsoft.Automation/automationAccounts/write | Grupo de Recursos |
| Microsoft.OperationalInsights/workspaces/write | Grupo de Recursos |

## <a name="components"></a>Componentes

Os VMs de início/paragem durante o horário de folga incluem livros de execução pré-configurados, horários e integração com registos do Monitor Azure. Pode utilizar estes elementos para adaptar o arranque e o encerramento dos seus VMs de acordo com as necessidades do seu negócio.

### <a name="runbooks"></a>Runbooks

A tabela seguinte lista os livros de execução que a funcionalidade implementa na sua conta Automation. NÃO efaça alterações no código do livro de execução. Em vez disso, escreva o seu próprio livro para novas funcionalidades.

> [!IMPORTANT]
> Não faça nenhum livro de corridas com **criança** anexada ao seu nome.

Todos os livros dos pais incluem o `WhatIf` parâmetro. Quando definido para True, o parâmetro suporta detalhar o comportamento exato que o livro de corridas toma quando executado sem o parâmetro e valida que os VMs corretos são direcionados. Um livro de execução só executa as suas ações definidas quando o `WhatIf` parâmetro está definido para Falso.

|Runbook | Parâmetros | Descrição|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> Ação de Alerta <br> WebHookURI | Ligou do livro dos pais. Este livro de execução cria alertas por recurso para o cenário de paragem automática.|
|AutoStop_CreateAlert_Parent | VMList<br> O que if: Verdadeiro ou Falso  | Cria ou atualiza as regras de alerta azure sobre VMs nos grupos de subscrição ou recursos direcionados. <br> `VMList`é uma lista separada de VMs. Por exemplo, `vm1, vm2, vm3`.<br> `WhatIf`permite a validação da lógica do livro de rcads sem executar.|
|AutoStop_Disable | Nenhum | Desativa alertas de paragem automática e horário predefinido.|
|AutoStop_VM_Child | WebHookData | Ligou do livro dos pais. As regras de alerta chamam a este livro de corridas para parar um VM clássico.|
|AutoStop_VM_Child_ARM | WebHookData |Ligou do livro dos pais. As regras de alerta chamam este livro de corridas para parar um VM.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Ação: Iniciar ou Parar<br> VMList  | Executa o início ou paragem de ação no grupo VM clássico pela Cloud Services. |
|ScheduledStartStop_Child | VMName <br> Ação: Iniciar ou Parar <br> ResourceGroupName | Ligou do livro dos pais. Executa uma ação de início ou paragem para a paragem programada.|
|ScheduledStartStop_Child_Classic | VMName<br> Ação: Iniciar ou Parar<br> ResourceGroupName | Ligou do livro dos pais. Executa uma ação de início ou paragem para a paragem programada para VMs clássicos. |
|ScheduledStartStop_Parent | Ação: Iniciar ou Parar <br>VMList <br> O que if: Verdadeiro ou Falso | Inicia ou para todos os VMs na subscrição. Editar as variáveis `External_Start_ResourceGroupNames` e `External_Stop_ResourceGroupNames` executar apenas nestes grupos de recursos direcionados. Também pode excluir VMs específicos atualizando a `External_ExcludeVMNames` variável.|
|SequencedStartStop_Parent | Ação: Iniciar ou Parar <br> O que if: Verdadeiro ou Falso<br>VMList| Cria tags nomeadas **sequencestart** e **sequencestop** em cada VM para a qual pretende sequenciar a atividade de arranque/paragem. Estes nomes são sensíveis aos casos. O valor da etiqueta deve ser um inteiro positivo (1, 2, 3) que corresponda à ordem em que pretende iniciar ou parar. <br>**Nota:** Os VMs devem estar dentro de grupos de recursos definidos `External_Start_ResourceGroupNames` em, `External_Stop_ResourceGroupNames` e `External_ExcludeVMNames` variáveis. Devem ter as etiquetas adequadas para que as ações possam produzir efeitos.|

### <a name="variables"></a>Variáveis

A tabela seguinte lista as variáveis criadas na sua conta De automação. Só modificar variáveis pré-fixas com `External` . Modificar variáveis pré-fixas com `Internal` efeitos indesejáveis.

> [!NOTE]
> As limitações ao nome vm e ao grupo de recursos são em grande parte resultado do tamanho variável. Ver [ativos Variáveis na Automação Azure.](https://docs.microsoft.com/azure/automation/shared-resources/variables)

|Variável | Descrição|
|---------|------------|
|External_AutoStop_Condition | O operador condicional necessário para configurar a condição antes de desencadear um alerta. Os valores aceitáveis `GreaterThan` `GreaterThanOrEqual` são, `LessThan` e `LessThanOrEqual` .|
|External_AutoStop_Description | O alerta para parar o VM se a percentagem de CPU exceder o limiar.|
|External_AutoStop_Frequency | A frequência de avaliação por regra. Este parâmetro aceita a entrada em formato timepan. Os valores possíveis são de 5 minutos a 6 horas. |
|External_AutoStop_MetricName | O nome da métrica de desempenho para a qual a regra de Alerta Azure deve ser configurado.|
|External_AutoStop_Severity | Gravidade do alerta métrico, que pode variar de 0 a 4. |
|External_AutoStop_Threshold | O limiar da regra de alerta azure especificado na variável `External_AutoStop_MetricName` . Os valores percentuais variam entre 1 e 100.|
|External_AutoStop_TimeAggregationOperator | O operador de agregação de tempo aplicado ao tamanho da janela selecionado para avaliar a circunstância. Os valores aceitáveis `Average` `Minimum` são, `Maximum` e `Total` `Last` .|
|External_AutoStop_TimeWindow | O tamanho da janela durante a qual Azure analisa métricas selecionadas para desencadear um alerta. Este parâmetro aceita a entrada em formato timepan. Os valores possíveis são de 5 minutos a 6 horas.|
|External_EnableClassicVMs| Valor especificando se os VMclássicos são alvo da funcionalidade. O valor padrão é verdadeiro. Detete esta variável para subscrições de Falso solução de nuvem (CSP). VMs clássicos requerem uma [corrida clássica Como conta](automation-create-standalone-account.md#create-a-classic-run-as-account).|
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
>Para a `External_WaitTimeForVMRetryInSeconds` variável, o valor predefinido foi atualizado de 600 para 2100. 

Em todos os cenários, as variáveis `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` , e são `External_ExcludeVMNames` necessárias para direcionar os VMs, exceto para as listas de VM separadas da vírlém para o **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**e **ScheduledStartStop_Parent** livros de execução. Ou seja, os seus VMs devem pertencer a grupos de recursos-alvo para que as ações de início e stop ocorram. A lógica funciona semelhante à Política Azure, na medida em que pode visar a subscrição ou grupo de recursos e ter ações herdadas por VMs recém-criados. Esta abordagem evita ter de manter um horário separado para cada VM e gerir partidas e paragens em escala.

### <a name="schedules"></a>Agendas

A tabela seguinte lista cada um dos horários predefinidos criados na sua conta Automation.Pode modificá-los ou criar os seus próprios horários personalizados.Por padrão, todos os horários são desativados, exceto para os horários **de Scheduled_StartVM** e **Scheduled_StopVM.**

Não permita todos os horários, porque fazê-lo pode criar ações de horário sucintas. É melhor determinar quais as otimizações que pretende fazer e modificá-las em conformidade. Consulte os cenários de exemplo na secção de visão geral para obter mais explicações.

|Nome da agenda | Frequência | Descrição|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | A cada 8 horas | Executa o **AutoStop_CreateAlert_Parent** livro a cada 8 horas, o que por sua vez para os valores baseados em VM em `External_Start_ResourceGroupNames` , e `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` variáveis. Em alternativa, pode especificar uma lista separada de VMs com a utilização do `VMList` parâmetro.|
|Scheduled_StopVM | Definido pelo utilizador, diariamente | Executa o **ScheduledStopStart_Parent** livro de corridas com um parâmetro de `Stop` todos os dias no momento especificado.Para automaticamente todos os VMs que cumprem as regras definidas por ativos variáveis.Ativar o **programado-startVM**relacionado .|
|Scheduled_StartVM | Definido pelo utilizador, diariamente | Executa o **livro de ScheduledStopStart_Parent** com um valor de parâmetro de cada dia no momento `Start` especificado. Inicia automaticamente todos os VMs que cumprem as regras definidas por ativos variáveis.Ativar o **programado-stopVM**relacionado .|
|Paragem sequenciada | 1:00 AM (UTC), todas as sextas-feiras | Executa o **livro de Sequenced_StopStop_Parent** com um valor de parâmetro de cada `Stop` sexta-feira no momento especificado.Sequencialmente (ascendente) para todos os VMs com uma etiqueta de **SequenceStop** definida pelas variáveis apropriadas. Para obter mais informações sobre valores de etiqueta e variáveis de ativos, consulte [Runbooks](#runbooks).Ativar o horário relacionado, **Sequenciado-StartVM**.|
|Iniciado-Arranque-VM | 13:00 (UTC), todas as segundas-feiras | Executa o **livro de SequencedStopStart_Parent** com um valor de parâmetro de cada `Start` segunda-feira no momento especificado. Sequencialmente (descendente) inicia todos os VMs com uma etiqueta de **SequenceStart** definida pelas variáveis apropriadas. Para obter mais informações sobre valores de etiquetae ativos variáveis, consulte [Runbooks](#runbooks). Ativar o horário relacionado, **Sequenced-StopVM**.

## <a name="use-the-feature-with-classic-vms"></a>Use a funcionalidade com VMs clássicos

Se estiver a utilizar os VMs Start/Stop durante o off-hours para VMs clássicos, a Automação processa todos os seus VMs sequencialmente por serviço na nuvem. Os VMs ainda são processados em paralelo através de diferentes serviços na nuvem. 

Para utilizar a funcionalidade com VMs clássicos, precisa de uma conta Classic Run As, que não é criada por defeito. Para obter instruções sobre a criação de uma conta Classic Run As, consulte [Create a Classic Run As account](automation-create-standalone-account.md#create-a-classic-run-as-account).

Se tem mais de 20 VMs por serviço na nuvem, aqui ficam algumas recomendações:

* Crie vários horários com o livro de execução dos pais **ScheduledStartStop_Parent** e especificando 20 VMs por programação. 
* Nas propriedades de horário, utilize o `VMList` parâmetro para especificar os nomes vm como uma lista separada da vírvia. 

Caso contrário, se o trabalho de Automação para esta funcionalidade dura mais de três horas, é temporariamente descarregado ou parado de acordo com o limite [de ações justas.](automation-runbook-execution.md#fair-share)

As subscrições do Azure CSP suportam apenas o modelo Do Gestor de Recursos Azure. Os serviços de Gestor de Recursos Não-Azure não estão disponíveis no programa. Quando os VMs iniciar/parar durante as operações fora de horas, poderá receber erros, uma vez que tem cmdlets para gerir recursos clássicos. Para saber mais sobre a CSP, consulte [os serviços disponíveis nas subscrições do CSP.](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services) Se utilizar uma subscrição de CSP, deverá definir a [variável External_EnableClassicVMs](#variables) para False após a implementação.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-feature"></a>Ativar a funcionalidade

Para começar a utilizar a funcionalidade, siga os passos em [Ativar VMs de arranque/paragem durante as horas de folga](automation-solution-vm-management-enable.md).

## <a name="view-the-feature"></a>Ver a funcionalidade

Utilize um dos seguintes mecanismos para aceder à funcionalidade ativada:

* Na sua conta de Automação, selecione **Start/Stop VM** em **Recursos Relacionados**. Na página Start/Stop VM, selecione **Gerir a solução** sob **soluções 'Gerir Start/Stop VM '**.

* Navegue para o espaço de trabalho log Analytics ligado à sua conta Deautomação. Depois de selecionar o espaço de trabalho, escolha **Soluções** do painel esquerdo. Na página Soluções, selecione **Start-Stop-VM[workspace]** da lista.  

A seleção da funcionalidade exibe a página Start-Stop-VM[workspace]. Aqui pode rever detalhes importantes, como as informações no azulejo **StartStopVM.** Tal como no seu espaço de trabalho log analytics, este azulejo apresenta uma contagem e uma representação gráfica dos trabalhos do livro de corridas para a funcionalidade que começou e terminou com sucesso.

![Página de Gestão de Atualização de Automatização](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Pode efetuar uma análise mais aprofundada dos registos de trabalho clicando no azulejo dodo. O painel mostra histórico de trabalho e consultas de pesquisa de registo predefinidas. Mude para o portal avançado de análise de registos para pesquisar com base nas suas consultas de pesquisa.

## <a name="update-the-feature"></a>Atualizar a funcionalidade

Se tiver implementado uma versão anterior de Start/Stop VMs durante o horário de folga, apague-o da sua conta antes de implementar uma versão atualizada. Siga os passos para [remover a função](#remove-the-feature) e, em seguida, siga os passos para [a ativar](automation-solution-vm-management-enable.md).

## <a name="remove-the-feature"></a>Remova a função

Se já não necessitar de utilizar a funcionalidade, pode eliminá-la da conta Automation. A eliminação da funcionalidade remove apenas os livros de execução associados. Não apaga os horários ou variáveis que foram criados quando a funcionalidade foi adicionada. 

Para eliminar Os VMs de arranque/paragem durante o horário de folga:

1. Na sua conta de Automação, selecione **Linked workspace** sob **recursos relacionados.**

2. Selecione **Ir para o espaço de trabalho**.

3. Clique em **Soluções** em **Geral**. 

4. Na página Soluções, selecione **Start-Stop-VM[Workspace]**. 

5. Na página VMManagementSolution[Workspace], selecione **Eliminar** a partir do menu.<br><br> ![Eliminar funcionalidade de gestão VM](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. Na janela Eliminar Solução, confirme se pretende eliminar a funcionalidade.

7. Enquanto a informação é verificada e a funcionalidade é eliminada, pode acompanhar o progresso no âmbito das Notificações , **escolhidas**a partir do menu. É devolvido à página Soluções após o processo de remoção.

8. A conta de Automação e o espaço de trabalho do Log Analytics não são eliminados como parte deste processo. Se não quiser manter o espaço de trabalho do Log Analytics, deve eliminá-lo manualmente do portal Azure:

    1. Procure e selecione espaços de **trabalho Log Analytics**.

    2. Na página do espaço de trabalho Log Analytics, selecione o espaço de trabalho.

    3. **Selecione Excluir** do menu.

    4. Se não quiser manter os componentes da funcionalidade de [funcionalidade](#components)supressão da automatização do Azure, pode eliminar manualmente cada um deles.

## <a name="next-steps"></a>Passos seguintes

Para ativar a funcionalidade em VMs no seu ambiente, consulte [Ativar Os VMs de arranque/paragem durante](automation-solution-vm-management-enable.md)as horas de folga .
