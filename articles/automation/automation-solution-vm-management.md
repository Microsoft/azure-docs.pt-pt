---
title: Azure Automation Start/Stop VMs durante a visão geral fora de horas
description: Este artigo descreve os VMs iniciar/parar durante o período de folga, que inicia ou para os VMs num horário e os monitoriza proactivamente a partir de Registos monitores do Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: b71e5b1a8ba5f3ee8f883c71a7221e01d4af4fb6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597713"
---
# <a name="startstop-vms-during-off-hours-overview"></a>VMs de início/paragem durante a visão geral fora de horas

Os VMs de início/paragem durante o início ou paragens ativadas ativadas Azure VMs. Inicia ou para as máquinas em horários definidos pelo utilizador, fornece insights através de registos do Azure Monitor e envia e-mails opcionais utilizando [grupos de ação](../azure-monitor/alerts/action-groups.md). A funcionalidade pode ser ativada tanto no Azure Resource Manager como em VMs clássicos para a maioria dos cenários.

Esta função utiliza [o cmdlet Start-AzVm](/powershell/module/az.compute/start-azvm) para iniciar VMs. Utiliza [o Stop-AzVM](/powershell/module/az.compute/stop-azvm) para parar os VMs.

> [!NOTE]
> Embora os livros tenham sido atualizados para utilizar os novos cmdlets do módulo Azure Az, utilizam o pseudónimo prefixo AzureRM.

> [!NOTE]
> Os VMs de início/paragem durante o horário de folga foram atualizados para suportar as versões mais recentes dos módulos Azure que estão disponíveis. A versão atualizada desta funcionalidade, disponível no Marketplace, não suporta os módulos AzureRM porque migramos de módulos AzureRM para Az.

A funcionalidade oferece uma opção de automação de baixo custo descentralizada para utilizadores que pretendam otimizar os seus custos de VM. Pode utilizar a funcionalidade para:

- [Agende os VMs para iniciar e parar](automation-solution-vm-management-config.md#schedule).
- Agende os VMs para iniciar e parar por ordem ascendente [utilizando tags Azure](automation-solution-vm-management-config.md#tags). Esta atividade não é suportada para VMs clássicos.
- VMs de parada automática com base na [baixa utilização do CPU](automation-solution-vm-management-config.md#cpuutil).

Seguem-se limitações com a característica atual:

- Gere VMs em qualquer região, mas só pode ser usado na mesma subscrição que a sua conta Azure Automation.
- Está disponível em Azure e Azure Government para qualquer região que suporte um espaço de trabalho Log Analytics, uma conta Azure Automation, e alertas. Atualmente, as regiões do Governo do Azure não suportam a funcionalidade de e-mail.

> [!NOTE]
> Antes de instalar esta versão, gostaríamos que soubesse sobre a [próxima versão](https://github.com/microsoft/startstopv2-deployments)– que está em pré-visualização neste momento.  Esta nova versão (V2) oferece todas as mesmas funcionalidades que esta, mas foi concebida para tirar partido da tecnologia mais recente em Azure. Adiciona algumas das funcionalidades geralmente solicitadas aos clientes, tais como suporte multi-subscrição a partir de uma única instância Start/Stop.

## <a name="prerequisites"></a>Pré-requisitos

- Os livros para os VMs de início/paragem durante o horário de folga funcionam com uma [conta Azure Run As](./automation-security-overview.md#run-as-accounts). A conta Run As é o método de autenticação preferencial porque utiliza a autenticação do certificado em vez de uma palavra-passe que pode expirar ou alterar com frequência.

- Um [espaço de trabalho do Azure Monitor Log Analytics](../azure-monitor/logs/design-logs-deployment.md) que armazena os registos de trabalho do runbook e o fluxo de trabalho resulta num espaço de trabalho para consultar e analisar. A conta Automation pode ser ligada a um novo ou existente espaço de trabalho Log Analytics, e ambos os recursos precisam estar no mesmo grupo de recursos.

Recomendamos que utilize uma conta de Automatização separada para trabalhar com VMs ativados para os VMs de início/paragem durante o período de folga. As versões do módulo Azure são frequentemente atualizadas e os seus parâmetros podem mudar. A funcionalidade não é atualizada na mesma cadência e pode não funcionar com versões mais recentes dos cmdlets que utiliza. Antes de importar os módulos atualizados para a sua conta de automação de produção, recomendamos que os importe numa conta de automatização de teste para verificar se não existem problemas de compatibilidade.

## <a name="permissions"></a>Permissões

Tem de ter certas permissões para ativar VMs para os VMs iniciar/parar durante o período de folga. As permissões são diferentes dependendo se a funcionalidade utiliza uma conta de Automação pré-criada e espaço de trabalho Log Analytics ou cria uma nova conta e espaço de trabalho.

Não precisa de configurar permissões se for um Contribuinte na subscrição e um Administrador Global no seu inquilino Azure Ative Directory (AD). Se não tiver esses direitos ou precisar de configurar um papel personalizado, certifique-se de que tem as permissões descritas abaixo.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Permissões para conta de Automação pré-existente e espaço de trabalho Log Analytics

Para ativar os VMs para os VMs iniciar/parar durante o período de folga utilizando uma conta de Automação existente e espaço de trabalho Log Analytics, necessita das seguintes permissões no âmbito do Grupo de Recursos. Para saber mais sobre papéis, consulte [os papéis personalizados da Azure.](../role-based-access-control/custom-roles.md)

| Permissão | Âmbito|
| --- | --- |
| Microsoft.Automation/automationAs contas/leitura | Grupo de Recursos |
| Microsoft.Automation/automationContagens/variáveis/escrita | Grupo de Recursos |
| Microsoft.Automation/automationContagens/horários/escrita | Grupo de Recursos |
| Microsoft.Automation/automationAs contas/runbooks/write | Grupo de Recursos |
| Microsoft.Automation/automationContagens/ligações/escrita | Grupo de Recursos |
| Microsoft.Automation/automationAs contas/certificados/escrita | Grupo de Recursos |
| Microsoft.Automation/automationContagens/módulos/escrita | Grupo de Recursos |
| Microsoft.Automation/automationContões/módulos/leitura | Grupo de Recursos |
| Microsoft.automation/automationAs contas/trabalhoSchedules/write | Grupo de Recursos |
| Microsoft.Automation/automationAs contas/empregos/escrita | Grupo de Recursos |
| Microsoft.Automation/automationAs contas/empregos/leitura | Grupo de Recursos |
| Microsoft.OperationsManagement/solutions/write | Grupo de Recursos |
| Microsoft.OperationalInsights/workspaces/* | Grupo de Recursos |
| Microsoft.Insights/diagnosticSettings/write | Grupo de Recursos |
| Microsoft.Insights/ActionGroups/Write | Grupo de Recursos |
| Microsoft.Insights/ActionGroups/read | Grupo de Recursos |
| Microsoft.Resources/subscrições/resourceGroups/read | Grupo de Recursos |
| Microsoft.Resources/implementações/* | Grupo de Recursos |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Permissões para nova conta de Automação e novo espaço de trabalho Log Analytics

Pode ativar VMs para os VMs iniciar/parar durante o período de folga utilizando uma nova conta de Automação e espaço de trabalho log Analytics. Neste caso, necessita das permissões definidas na secção anterior, bem como das permissões definidas nesta secção. Também precisa das seguintes funções:

- Co-Administrator na subscrição. Esta função é necessária para criar a conta Classic Run As se você vai gerir VMs clássicos. [As contas Classic Run As](automation-create-standalone-account.md#create-a-classic-run-as-account) já não são criadas por defeito.
- Adesão ao papel de Promotor de Aplicações [AD Azure.](../active-directory/roles/permissions-reference.md) Para obter mais informações sobre a configuração de Run As Accounts, consulte [permissões para configurar executar como contas](automation-security-overview.md#permissions).
- Colaborador na subscrição ou nas seguintes permissões.

| Permissão |Âmbito|
| --- | --- |
| Microsoft.Autorização/Operações/ler | Subscrição|
| Microsoft.Autorização/permissões/ler |Subscrição|
| Microsoft.Autorização/funAsignments/read | Subscrição |
| Microsoft.Authorization/roleAssignments/write | Subscrição |
| Microsoft.Autorização/funAsignments/delete | Subscrição |
| Microsoft.Automation/automationContões/ligações/leitura | Grupo de Recursos |
| Microsoft.Automation/automationAs contas/certificados/leitura | Grupo de Recursos |
| Microsoft.Automation/automationAs contas/escrita | Grupo de Recursos |
| Microsoft.OperationalInsights/workspaces/write | Grupo de Recursos |

## <a name="components"></a>Componentes

Os VMs de início/paragem durante o período de folga incluem livros de execução pré-configurados, horários e integração com registos do Monitor Azure. Pode utilizar estes elementos para adaptar o arranque e o encerramento dos seus VMs de acordo com as suas necessidades de negócio.

### <a name="runbooks"></a>Runbooks

A tabela que se segue lista os livros que a funcionalidade implementa na sua conta Dem automação. NÃO erose alterações no código do livro de bordo. Em vez disso, escreva o seu próprio livro de execução para uma nova funcionalidade.

> [!IMPORTANT]
> Não execute diretamente nenhum livro com **criança** anexada ao seu nome.

Todos os livros de bordo dos pais incluem o `WhatIf` parâmetro. Quando definido para True, o parâmetro suporta detalhar o comportamento exato que o livro de execução leva quando executado sem o parâmetro e valida que os VMs corretos são alvo. Um livro de bordo só executa as suas ações definidas quando o `WhatIf` parâmetro é definido como Falso.

|Runbook | Parâmetros | Description|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertaAção <br> WebHookURI | Liga do livro dos pais. Este runbook cria alertas por recurso para o cenário de paragem automática.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: Verdadeiro ou Falso  | Cria ou atualiza as regras de alerta Azure em VMs nos grupos de subscrição ou recursos direcionados. <br> `VMList` é uma lista separada por vm (sem espaços em branco), por exemplo, `vm1,vm2,vm3` .<br> `WhatIf` permite validação da lógica do runbook sem executar.|
|AutoStop_Disable | Nenhum | Desativa os alertas de paragem automática e o horário predefinido.|
|AutoStop_VM_Child | WebHookData | Liga do livro dos pais. As regras de alerta chamam este runbook para parar um VM clássico.|
|AutoStop_VM_Child_ARM | WebHookData |Liga do livro dos pais. As regras de alerta chamam este livro de bordo para parar um VM.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Ação: Iniciar ou Parar<br> VMList  | Executa início de ação ou paragem no grupo VM clássico da Cloud Services. |
|ScheduledStartStop_Child | VMName <br> Ação: Iniciar ou Parar <br> ResourceGroupName | Liga do livro dos pais. Executa uma ação inicial ou de paragem para a paragem programada.|
|ScheduledStartStop_Child_Classic | VMName<br> Ação: Iniciar ou Parar<br> ResourceGroupName | Liga do livro dos pais. Executa uma ação inicial ou stop para a paragem programada para VMs clássicos. |
|ScheduledStartStop_Parent | Ação: Iniciar ou Parar <br>VMList <br> WhatIf: Verdadeiro ou Falso | Começa ou para todos os VMs na subscrição. Editar as variáveis `External_Start_ResourceGroupNames` e `External_Stop_ResourceGroupNames` executar apenas nestes grupos de recursos direcionados. Também pode excluir VMs específicos atualizando a `External_ExcludeVMNames` variável.|
|SequencedStartStop_Parent | Ação: Iniciar ou Parar <br> WhatIf: Verdadeiro ou Falso<br>VMList| Cria tags nomeadas **início de sequência** e **sequências paragens** em cada VM para o qual pretende sequenciar a atividade de início/paragem. Estes nomes são sensíveis a casos. O valor da etiqueta deve ser uma lista de inteiros positivos, por exemplo, `1,2,3` que corresponde à ordem em que pretende iniciar ou parar. <br>**Nota:** Os VM devem estar dentro de grupos de recursos definidos em `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` , e `External_ExcludeVMNames` variáveis. Devem ter as etiquetas adequadas para que as ações produzam efeitos.|

### <a name="variables"></a>Variáveis

A tabela seguinte lista as variáveis criadas na sua conta Automation. Apenas modificar variáveis prefixadas com `External` . Modificar variáveis prefixadas com `Internal` efeitos indesejáveis.

> [!NOTE]
> As limitações no nome VM e no grupo de recursos são em grande parte resultado de tamanho variável. Ver [ativos variáveis na Azure Automation](./shared-resources/variables.md).

|Variável | Descrição|
|---------|------------|
|External_AutoStop_Condition | O operador condicional necessário para configurar a condição antes de desencadear um alerta. Valores aceitáveis `GreaterThan` `GreaterThanOrEqual` `LessThan` são, , e `LessThanOrEqual` .|
|External_AutoStop_Description | O alerta para parar o VM se a percentagem de CPU exceder o limiar.|
|External_AutoStop_Frequency | A frequência de avaliação da regra. Este parâmetro aceita a entrada no formato timepan. Os valores possíveis são de 5 minutos a 6 horas. |
|External_AutoStop_MetricName | O nome da métrica de desempenho para a qual a regra de Alerta Azure deve ser configurada.|
|External_AutoStop_Severity | Gravidade do alerta métrico, que pode variar de 0 a 4. |
|External_AutoStop_Threshold | O limiar da regra de Alerta Azure especificado na variável `External_AutoStop_MetricName` . Os valores percentuais variam de 1 a 100.|
|External_AutoStop_TimeAggregationOperator | O operador de agregação de tempo aplicado ao tamanho da janela selecionado para avaliar a condição. Valores aceitáveis `Average` são, `Minimum` , , e `Maximum` `Total` `Last` .|
|External_AutoStop_TimeWindow | O tamanho da janela durante a qual a Azure analisa métricas selecionadas para desencadear um alerta. Este parâmetro aceita a entrada no formato timepan. Os valores possíveis são de 5 minutos a 6 horas.|
|External_EnableClassicVMs| Valor especificando se os VM clássicos são alvo da funcionalidade. O valor predefinido é verdadeiro. Desave esta variável para subscrições falsas para o Azure Cloud Solution Provider (CSP). Os VMs clássicos requerem uma [conta Classic Run As](automation-create-standalone-account.md#create-a-classic-run-as-account).|
|External_ExcludeVMNames | Lista separada por vma de nomes VM para excluir, limitada a 140 VMs. Se adicionar mais de 140 VMs à lista, os VM especificados para exclusão podem ser inadvertidamente iniciados ou parados.|
|External_Start_ResourceGroupNames | Lista separada por vírgula de um ou mais grupos de recursos que são direcionados para ações de início.|
|External_Stop_ResourceGroupNames | Lista separada por vírgula de um ou mais grupos de recursos que são direcionados para ações de paragem.|
|External_WaitTimeForVMRetrySeconds |O tempo de espera em segundos para que as ações sejam executadas nos VMs para o **SequencedStartStop_Parent** livro de recortes. Esta variável permite que o livro de bordo aguarde as operações infantis por um número especificado de segundos antes de prosseguir com a ação seguinte. O tempo máximo de espera é de 10800, ou três horas. O valor predefinido é de 2100 segundos.|
|Internal_AutomationAccountName | Especifica o nome da conta Automation.|
|Internal_AutoSnooze_ARM_WebhookURI | O webhook URI apelou para o cenário AutoStop para VMs.|
|Internal_AutoSnooze_WebhookUri | O webhook URI apelou ao cenário AutoStop para VMs clássicos.|
|Internal_AzureSubscriptionId | O ID de assinatura Azure.|
|Internal_ResourceGroupName | O nome do grupo de recursos de conta Automation.|

>[!NOTE]
>Para a `External_WaitTimeForVMRetryInSeconds` variável, o valor predefinido foi atualizado de 600 a 2100.

Em todos os cenários, as variáveis `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` , e são `External_ExcludeVMNames` necessárias para direcionar os VMs, com exceção das listas de VM separadas por **vm** para o AutoStop_CreateAlert_Parent , **SequencedStartStop_Parent**, e **ScheduledStartStop_Parent.** Ou seja, os seus VMs devem pertencer a grupos de recursos-alvo para iniciar e parar as ações a ocorrer. A lógica funciona semelhante à Azure Policy, na medida em que pode direcionar o grupo de subscrição ou recursos e ter ações herdadas por VMs recém-criados. Esta abordagem evita ter de manter um horário separado para cada VM e gerir arranques e paragens em escala.

### <a name="schedules"></a>Agendas

A tabela que se segue lista cada um dos horários predefinidos criados na sua conta Automation. Pode modificá-los ou criar os seus próprios horários personalizados. Por predefinição, todos os horários são desativado, exceto os horários **de Scheduled_StartVM** e **Scheduled_StopVM.**

Não ative todos os horários, porque fazê-lo pode criar ações de horário sobrepostas. É melhor determinar quais as otimizações que pretende fazer e modificá-las em conformidade. Consulte os cenários de exemplo na secção geral para obter mais explicações.

|Nome da agenda | Frequência | Description|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | A cada 8 horas | Executa o **AutoStop_CreateAlert_Parent** livro de 8 horas, o que por sua vez para os valores baseados em VM em `External_Start_ResourceGroupNames` , e `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` variáveis. Em alternativa, pode especificar uma lista de VMs separadas por vírgula utilizando o `VMList` parâmetro.|
|Scheduled_StopVM | Definido pelo utilizador, diariamente | Executa o **ScheduledStopStart_Parent** livro de bordo com um parâmetro de `Stop` todos os dias na hora especificada. Para automaticamente todos os VMs que cumprem as regras definidas por ativos variáveis. Ativar o horário relacionado **Programado-StartVM**.|
|Scheduled_StartVM | Definido pelo utilizador, diariamente | Executa o **ScheduledStopStart_Parent** livro com um valor de parâmetro de `Start` cada dia na hora especificada. Inicia automaticamente todos os VMs que cumprem as regras definidas por ativos variáveis. Ativar o horário relacionado **Agendado-StopVM**.|
|Sequenced-StopVM | 1:00 AM (UTC), todas as sextas-feiras | Executa o **Sequenced_StopStop_Parent** runbook com um valor de parâmetro de `Stop` todas as sextas-feiras no momento especificado. Sequencialmente (ascendente) para todos os VMs com uma etiqueta de **SequenceStop** definida pelas variáveis apropriadas. Para obter mais informações sobre valores de etiquetas e variáveis de ativos, consulte [Runbooks](#runbooks). Ativar o horário relacionado, **Sequenciado-StartVM**.|
|Sequenced-StartVM | 13:00 PM (UTC), todas as segundas-feiras | Executa o **SequencedStopStart_Parent** livro com um valor de parâmetro de `Start` todas as segundas-feiras na hora especificada. Sequencialmente (descendente) inicia todos os VMs com uma etiqueta de **SequenceStart** definida pelas variáveis apropriadas. Para obter mais informações sobre os valores da etiqueta e os ativos variáveis, consulte [Runbooks](#runbooks). Ativar o horário relacionado, **Sequenciado-StopVM**.|

## <a name="use-the-feature-with-classic-vms"></a>Use a funcionalidade com VMs clássicos

Se estiver a utilizar os VMs iniciar/parar durante o período de folga para VMs clássicos, a Automatização processa todos os seus VMs sequencialmente por serviço na nuvem. Os VMs ainda são processados em paralelo em diferentes serviços na nuvem. 

Para utilizar a funcionalidade com VMs clássicos, precisa de uma conta Classic Run As, que não é criada por padrão. Para obter instruções sobre a criação de uma conta Classic Run As, consulte [Criar uma conta Classic Run As](automation-create-standalone-account.md#create-a-classic-run-as-account).

Se tiver mais de 20 VMs por serviço na nuvem, aqui ficam algumas recomendações:

* Crie vários horários com o ScheduledStartStop_Parent do livro de **bordo** dos pais e especificando 20 VMs por programação.
* Nas propriedades do agendamento, utilize o `VMList` parâmetro para especificar os nomes VM como uma lista separada por vírgula (sem espaços em branco).

Caso contrário, se o trabalho de Automação para esta funcionalidade for superior a três horas, é temporariamente descarregado ou parado de acordo com o limite [de quota justa.](automation-runbook-execution.md#fair-share)

As subscrições Azure CSP suportam apenas o modelo Azure Resource Manager. Os serviços não-Azure Resource Manager não estão disponíveis no programa. Quando os VMs iniciar/parar durante o período de funcionamento, poderá receber erros, uma vez que tem cmdlets para gerir recursos clássicos. Para saber mais sobre a CSP, consulte [os serviços disponíveis em assinaturas CSP.](/azure/cloud-solution-provider/overview/azure-csp-available-services) Se utilizar uma subscrição CSP, deverá definir a [variável External_EnableClassicVMs](#variables) para Falso após a implementação.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="view-the-feature"></a>Ver a funcionalidade

Utilize um dos seguintes mecanismos para aceder à função ativada:

* Na sua conta de Automação, selecione **Iniciar/Parar VM** em **Recursos Relacionados**. Na página Iniciar/Parar VM, **selecione Gerir a solução** em **Gestão de Soluções VM iniciar/parar**.

* Navegue para o espaço de trabalho Log Analytics ligado à sua conta Automation. Depois de selecionar o espaço de trabalho, escolha **Soluções** do painel esquerdo. Na página Soluções, selecione **Start-Stop-VM [espaço de trabalho]** da lista.  

A seleção da função apresenta a página **Start-Stop-VM[espaço de trabalho].** Aqui pode rever detalhes importantes, como as informações no azulejo **StartStopVM.** Tal como no seu espaço de trabalho Log Analytics, este azulejo apresenta uma contagem e uma representação gráfica dos trabalhos de runbook para a funcionalidade que começaram e terminaram com sucesso.

![Página de Gestão de Atualização de Automação](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Pode efetuar uma análise mais aprofundada dos registos de trabalho clicando no azulejo do donut. O painel de instrumentos mostra o histórico de trabalho e consultas de pesquisa de registos predefinidas. Mude para o portal avançado de análise de registo para pesquisar com base nas suas consultas de pesquisa.

## <a name="update-the-feature"></a>Atualizar a funcionalidade

Se tiver implementado uma versão anterior de VMs iniciar/parar durante o horário de folga, elimine-a da sua conta antes de implementar uma versão atualizada. Siga os passos para [remover a função](automation-solution-vm-management-remove.md#delete-the-feature) e, em seguida, siga os passos para [o ativar](automation-solution-vm-management-enable.md).

## <a name="next-steps"></a>Passos seguintes

Para ativar a funcionalidade em VMs no seu ambiente, consulte [Ativar VMs de arranque/paragem durante as horas de folga](automation-solution-vm-management-enable.md).