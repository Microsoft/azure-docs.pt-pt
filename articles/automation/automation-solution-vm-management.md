---
title: VMs de arranque/paragem durante a solução off-hours
description: Esta solução de gestão VM inicia e para as máquinas virtuais do Gestor de Recursos Azure numa programação e monitoriza proativamente a partir de registos do Monitor Azure.
services: automation
ms.subservice: process-automation
ms.date: 02/25/2020
ms.topic: conceptual
ms.openlocfilehash: cbf181b9a6d3860854c7b61cca0e6c50810cced9
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373163"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Start/Stop VMs durante solução off-hours na Automação Azure

Os VMs start/stop durante o início da solução off-hours e para as suas máquinas virtuais Azure em horários definidos pelo utilizador, fornece informações através de registos do Monitor Azure e envia e-mails opcionais utilizando [grupos](../azure-monitor/platform/action-groups.md)de ação . Suporta tanto o Gestor de Recursos Azure como os VMs clássicos para a maioria dos cenários. Para utilizar esta solução com VMs clássicos, precisa de uma conta Classic RunAs, que não é criada por defeito. Para obter instruções sobre a criação de uma conta RunAs [Clássicas, consulte contas Clássicas Run-As](automation-create-standalone-account.md#classic-run-as-accounts).

> [!NOTE]
> Os VMs Start/Stop durante a solução off-hours foram testados com os módulos Azure que são importados para a sua Conta de Automação quando implementa a solução. A tualmente, a solução não funciona com versões mais recentes do módulo Azure. Isto só afeta a Conta de Automação que utiliza para executar os VMs start/stop durante a solução off-hours. Ainda pode utilizar versões mais recentes do módulo Azure nas suas outras Contas de Automação, conforme descrito em [Como atualizar módulos Azure PowerShell em Automação Azure](automation-update-azure-modules.md)

Esta solução fornece uma opção de automação descentralizada de baixo custo para os utilizadores que pretendam otimizar os seus custos vm. Com esta solução, pode:

- Agende VMs para começar e parar.
- Agendar VMs para iniciar e parar em ordem ascendente usando Tags Azure (não suportados para VMs clássicos).
- VMs de paragem automática com base na baixa utilização do CPU.

Seguem-se as limitações com a solução atual:

- Esta solução gere VMs em qualquer região, mas só pode ser usada na mesma subscrição que a sua conta Azure Automation.
- Esta solução está disponível em Azure e AzureGov para qualquer região que suporte um espaço de trabalho Log Analytics, uma conta Azure Automation e Alerts. Atualmente, as regiões azureGov não suportam a funcionalidade de e-mail.

> [!NOTE]
> Se estiver a usar a solução para VMs clássicos, então todos os seus VMs serão processados sequencialmente por serviço de nuvem. As máquinas virtuais ainda são processadas paralelamente através de diferentes serviços de nuvem. Se tiver mais de 20 VMs por serviço na nuvem, recomendamos a criação de vários horários com o livro de executantes dos pais **ScheduledStartStop_Parent** e especificar 20 VMs por horário. Nas propriedades de horário, especificar como uma lista separada de vírinas, nomes VM no parâmetro **VMList.** Caso contrário, se o trabalho de Automação para esta solução demorar mais de três horas, é temporariamente descarregado ou parado de acordo com o limite [de ações justas.](automation-runbook-execution.md#fair-share)
>
> As subscrições do Azure Cloud Solution Provider (Azure CSP) suportam apenas o modelo Do Gestor de Recursos Azure, não estão disponíveis serviços de Gestor de Recursos não Azure no programa. Quando a solução Iniciar/Parar funciona poderá receber erros, uma vez que tem cmdlets para gerir recursos clássicos. Para saber mais sobre a CSP, consulte [os serviços disponíveis nas subscrições do CSP.](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services) Se utilizar uma subscrição de CSP, deverá modificar a [**variável External_EnableClassicVMs**](#variables) para **Falsa** após a implementação.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Os livros de execução desta solução funcionam com uma [conta Azure Run As](automation-create-runas-account.md). O Executar Como conta é o método de autenticação preferido, pois utiliza autenticação de certificado em vez de uma palavra-passe que pode expirar ou alterar frequentemente.

Recomendamos que utilize uma Conta de Automação separada para a solução Start/Stop VM. Isto porque as versões do módulo Azure são frequentemente atualizadas e os seus parâmetros podem mudar. A solução Start/Stop VM não é atualizada na mesma cadência, pelo que pode não funcionar com versões mais recentes dos cmdlets que utiliza. Recomendamos também que teste as atualizações do módulo numa Conta de Automatização de Teste antes de as importar na sua Conta de Automação de Produção.

### <a name="permissions-needed-to-deploy"></a>Permissões necessárias para implementar

Existem certas permissões que um utilizador deve ter de implementar os VMs Start/Stop durante a solução de horas de folga. Estas permissões são diferentes se utilizar uma conta de automação pré-criada e log analytics espaço de trabalho ou criar novas durante a implementação. Se é colaborador na subscrição e administrador global no seu inquilino do Diretório Ativo Azure, não precisa de configurar as seguintes permissões. Se não tiver esses direitos ou precisar de configurar uma função personalizada, consulte as permissões exigidas abaixo.

#### <a name="pre-existing-automation-account-and-log-analytics-workspace"></a>Conta de Automação pré-existente e espaço de trabalho log Analytics

Para implementar os VMs de início/paragem durante o horário de folga para uma conta de automação existente e espaço de trabalho log Analytics, o utilizador que implementa a solução requer as seguintes permissões no **Grupo de Recursos**. Para saber mais sobre papéis, consulte [papéis personalizados para os recursos do Azure.](../role-based-access-control/custom-roles.md)

| Permissão | Âmbito|
| --- | --- |
| Microsoft.Automation/automationAccounts/read | Grupo de Recursos |
| Microsoft.Automation/automationAccounts/variables/write | Grupo de Recursos |
| Microsoft.Automation/automationAccounts/schedules/write | Grupo de Recursos |
| Microsoft.Automation/automationAccounts/runbooks/write | Grupo de Recursos |
| Microsoft.Automation/automationAccounts/connections/write | Grupo de Recursos |
| Microsoft.Automation/automationAccounts/certificates/write | Grupo de Recursos |
| Microsoft.Automation/automationAccounts/modules/write | Grupo de Recursos |
| Microsoft.Automation/automationAccounts/modules/read | Grupo de Recursos |
| Microsoft.automation/automationAccounts/jobSchedules/write | Grupo de Recursos |
| Microsoft.Automation/automationAccounts/jobs/write | Grupo de Recursos |
| Microsoft.Automation/automationAccounts/jobs/read | Grupo de Recursos |
| Microsoft.OperationsManagement/solutions/write | Grupo de Recursos |
| Microsoft.OperationalInsights/workspaces/* | Grupo de Recursos |
| Microsoft.Insights/diagnosticSettings/write | Grupo de Recursos |
| Microsoft.Insights/ActionGroups/Write | Grupo de Recursos |
| Microsoft.Insights/ActionGroups/read | Grupo de Recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupo de Recursos |
| Microsoft.Resources/deployments/* | Grupo de Recursos |

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>Nova Conta de Automação e um novo espaço de trabalho log Analytics

Para implementar os VMs de início/paragem durante o horário de folga para uma nova Conta de Automação e espaço de trabalho log Analytics, o utilizador que implementa a solução necessita das permissões definidas na secção anterior, bem como as seguintes permissões:

- Coadministrador na subscrição - Só é necessário criar o Classic Run As Account se você vai gerir VMs clássicos. [As Contas RunAs Clássicas](automation-create-standalone-account.md#classic-run-as-accounts) já não são criadas por padrão.
- Membro do papel de Desenvolvedor de **Aplicações** de [Diretório Ativo Azure.](../active-directory/users-groups-roles/directory-assign-admin-roles.md) Para obter mais informações sobre a configuração de Executar Como Contas, consulte [Permissões para configurar Executar Como contas](manage-runas-account.md#permissions).
- Colaborador na subscrição ou nas seguintes permissões.

| Permissão |Âmbito|
| --- | --- |
| Microsoft.Authorization/Operations/read | Subscrição|
| Microsoft.Authorization/permissions/read |Subscrição|
| Microsoft.Authorization/roleAssignments/read | Subscrição |
| Microsoft.Authorization/roleAssignments/write | Subscrição |
| Microsoft.Authorization/roleAssignments/delete | Subscrição |
| Microsoft.Automation/automationAccounts/connections/read | Grupo de Recursos |
| Microsoft.Automation/automationAccounts/certificates/read | Grupo de Recursos |
| Microsoft.Automation/automationAccounts/write | Grupo de Recursos |
| Microsoft.OperationalInsights/workspaces/write | Grupo de Recursos |

## <a name="deploy-the-solution"></a>Implementar a solução

Execute os seguintes passos para adicionar os VMs de início/paragem durante a solução off-hours à sua conta Automation e, em seguida, configurar as variáveis para personalizar a solução.

1. A partir de uma Conta de Automação, selecione **Start/Stop VM** em **Recursos Relacionados**. A partir daqui, pode clicar **em Saber mais sobre e ativar a solução**. Se já tiver uma solução Start/Stop VM implementada, pode selecioná-la clicando em **Gerir a solução** e encontrando-a na lista.

   ![Ativar a partir da conta de automação](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Também pode criá-lo a partir de qualquer lugar do portal Azure, clicando em **Criar um recurso**. Na página marketplace, digite uma palavra-chave como **Iniciar** ou **Iniciar/Parar**. À medida que começa a escrever, a lista filtra com base na sua entrada. Em alternativa, pode escrever uma ou mais palavras-chave a partir do nome completo da solução e, em seguida, prima Enter. Selecione **VMs iniciar/parar durante** o horário de folga a partir dos resultados da pesquisa.

2. Nos **VMs iniciar/parar durante a página off-hours** para a solução selecionada, reveja as informações sumárias e, em seguida, clique em **Criar**.

   ![Portal do Azure](media/automation-solution-vm-management/azure-portal-01.png)

3. Aparece a página **Add Solution.** É-lhe pedido que configure a solução antes de a importar para a sua subscrição de Automação.

   ![Página de Soluções de Adição de Gestão VM](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

4. Na página **Adicionar Solução,** selecione **Workspace**. Selecione um espaço de trabalho log Analytics ligado à mesma subscrição Azure em que a conta Automation está. Se não tiver um espaço de trabalho, selecione **Criar novo espaço**de trabalho . Na página do **espaço de trabalho Log Analytics,** execute os seguintes passos:
   - Especifique um nome para o novo espaço de **trabalho log analytics,** como "ContosoLAWorkspace".
   - Selecione uma **Subscrição** para ligar selecionando a partir da lista de drop-down, se o padrão selecionado não for apropriado.
   - Para **o Grupo de Recursos,** pode criar um novo grupo de recursos ou selecionar um existente.
   - Selecione uma **Localização**.
   - Selecione um **Escalão de preço**. Escolha a opção **Per GB (Autónoma).** Os registos do Monitor Azure têm [preços](https://azure.microsoft.com/pricing/details/log-analytics/) atualizados e o nível Per GB é a única opção.

   > [!NOTE]
   > Quando ativar soluções, apenas são suportadas determinadas regiões para associar uma área de trabalho do Log Analytics e uma Conta de Automatização.
   >
   > Para obter uma lista dos pares de mapeamento suportados, consulte [O Mapeamento da Região para A Conta de Automação e espaço de trabalho log Analytics](how-to/region-mappings.md).

5. Depois de fornecer as informações necessárias na página **do espaço de trabalho Log Analytics,** clique em **Criar**. Pode acompanhar o seu progresso no âmbito de **Notificações** do menu, que o devolve à página **Add Solution** quando feito.
6. Na página **Add Solution,** selecione **conta Automation**. Se estiver a criar um novo espaço de trabalho log Analytics, pode criar uma nova conta Automation para estar associada a ela, ou selecionar uma Conta de Automação existente que ainda não esteja ligada a um espaço de trabalho do Log Analytics. Selecione uma Conta de Automação existente ou clique **Criar uma conta De Automação,** e na página da conta Add **Automation,** forneça as seguintes informações:
   - No campo **Nome**, introduza o nome da conta de Automatização.

     Todas as outras opções são automaticamente povoadas com base no espaço de trabalho do Log Analytics selecionado. Estas opções não podem ser modificadas. O método de autenticação predefinido para os runbooks incluídos nesta solução é a conta Run As do Azure. Depois de clicar em **OK,** as opções de configuração são validadas e a conta Automation é criada. Pode acompanhar o progresso em **Notificações**, no menu.

7. Finalmente, na página **Adicionar Solução,** selecione **Configuração**. A página **dos Parâmetros** aparece.

   ![Página de parâmetros para solução](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Aqui, é solicitado a:
   - Especifique os **nomes**do Grupo de Recursos Alvo . Estes valores são nomes de grupos de recursos que contêm VMs para serem geridos por esta solução. Pode introduzir mais do que um nome e separar cada um utilizando uma vírvia (os valores não são sensíveis a casos). Se quiser segmentar todas as VMs em todos os grupos de recursos da subscrição, a utilização de um caráter universal é suportada. Este valor é armazenado nas variáveis **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames.**
   - Especifique a Lista de **Exclusão vm (cadeia)** . Este valor é o nome de uma ou mais máquinas virtuais do grupo de recursos alvo. Pode introduzir mais do que um nome e separar cada um utilizando uma vírvia (os valores não são sensíveis a casos). A utilização de um wildcard é suportada. Este valor é armazenado na **variável External_ExcludeVMNames.**
   - Selecione um **Horário**. Selecione uma data e hora para o seu horário. Será criado um horário diário recorrente a partir do tempo que selecionou. A seleção de uma região diferente não está disponível. Para configurar o horário para o seu fuso horário específico depois de configurar a solução, consulte modificar o horário de [arranque e encerramento](#modify-the-startup-and-shutdown-schedules).
   - Para receber notificações de **e-mail** de um grupo de ação, aceite o valor padrão de **Sim** e forneça um endereço de e-mail válido. Se selecionar **Não** mas decidir mais tarde que deseja receber notificações de e-mail, pode atualizar o grupo de [ação](../azure-monitor/platform/action-groups.md) que é criado com endereços de e-mail válidos separados por uma vírem. Também precisa de ativar as seguintes regras de alerta:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > O valor padrão para nomes de grupos de **recursos-alvo** é um **&ast;** . Isto visa todos os VMs numa subscrição. Se não quiser que a solução para direcionar todos os VMs da sua subscrição, este valor precisa de ser atualizado para uma lista de nomes de grupos de recursos antes de ativar os horários.

8. Depois de configurar as definições iniciais necessárias para a solução, clique em **OK** para fechar a página **Parâmetros** e selecione **Criar**. Depois de todas as definições serem validadas, a solução é implementada na sua subscrição. Este processo pode demorar alguns segundos a terminar, e pode acompanhar o seu progresso ao abrigo de **Notificações** do menu.

> [!NOTE]
> Se tiver uma subscrição do Azure Cloud Solution Provider (Azure Cloud Solution Provider), após a implementação estar concluída, na sua Conta de Automação, vá a **Variáveis** ao abrigo **de Recursos Partilhados** e detetete a [**variável External_EnableClassicVMs**](#variables) para **Falso**. Isto impede a solução de procurar recursos VM clássicos.

## <a name="scenarios"></a>Cenários

A solução contém três cenários distintos. Estes cenários são:

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>Cenário 1: Start/Stop VMs em horário

Este cenário é a configuração padrão quando se implementa a solução pela primeira vez. Por exemplo, pode configurá-lo para parar todos os VMs através de uma subscrição quando sair do trabalho à noite, e iniciá-los de manhã quando estiver de volta ao escritório. Quando configuraos os horários **Programados-StartVM** e **Scheduled-StopVM** durante a implementação, iniciam e param os VMs direcionados. Configurar esta solução para parar vMs é suportado, consulte [Modificar os horários de arranque e encerramento](#modify-the-startup-and-shutdown-schedules) para aprender a configurar um horário personalizado.

> [!NOTE]
> O fuso horário é o seu fuso horário atual quando configura o parâmetro de tempo de programação. No entanto, encontra-se armazenado em formato UTC na Automação Azure. Não é necessário fazer qualquer conversão de fuso horário, uma vez que esta é manipulada durante a implantação.

Controla quais os VMs que estão no âmbito configurando as seguintes variáveis: **External_Start_ResourceGroupNames,** **External_Stop_ResourceGroupNames**e **External_ExcludeVMNames**.

Pode permitir que se direcione a ação contra um grupo de subscrição e recursos, ou direcione uma lista específica de VMs, mas não ambos.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Direcione as ações de início e paragem contra um grupo de subscrição e recursos

1. Configure as variáveis **External_Stop_ResourceGroupNames** e **External_ExcludeVMNames** para especificar os VMs-alvo.
2. Ative e atualize os horários **de Arranque Agendadoe** e **Paragem Programada-StopVM.**
3. Execute o **livro de ScheduledStartStop_Parent** com o parâmetro ACTION definido para **iniciar** e o parâmetro WHATIF definido para **True** para pré-visualizar as suas alterações.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Direcione a ação de início e paragem pela lista VM

1. Executar o **livro de** ScheduledStartStop_Parent com o parâmetro ACTION definido para **iniciar,** adicionar uma lista separada de VMs no parâmetro *VMList* e, em seguida, definir o parâmetro WHATIF para **True**. Pré-visualizar as suas alterações.
1. Configure o parâmetro **External_ExcludeVMNames** com uma lista separada de VMs (VM1, VM2, VM3).
1. Este cenário não honra as variáveis **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupnames.** Para este cenário, é necessário criar o seu próprio horário de Automação. Para mais detalhes, consulte [Agendar um livro de execução em Automação Azure.](../automation/automation-schedules.md)

> [!NOTE]
> O valor para nomes de **grupode recursos-alvo** é armazenado como o valor tanto para **External_Start_ResourceGroupNames** como para **External_Stop_ResourceGroupNames**. Para maior granularidade, pode modificar cada uma destas variáveis para direcionar diferentes grupos de recursos. Para iniciar a ação, utilize **External_Start_ResourceGroupNames**, e para parar a ação, utilize **External_Stop_ResourceGroupNames**. Os VMs são automaticamente adicionados aos horários de início e paragem.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>Cenário 2: Iniciar/Parar VMS em sequência utilizando tags

Num ambiente que inclui dois ou mais componentes em múltiplas VMs suportando uma carga de trabalho distribuída, suportando a sequência em que os componentes são iniciados e parados por ordem é importante. Pode realizar este cenário executando os seguintes passos:

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Direcione as ações de início e paragem contra um grupo de subscrição e recursos

1. Adicione um **início** de sequência e uma etiqueta de **sequência** stop com um valor inteiro positivo para VMs que são direcionados em **variáveis External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames.** As ações de início e paragem são realizadas em ordem ascendente. Para aprender a marcar um VM, consulte [a Tag a Windows Virtual Machine em Azure](../virtual-machines/windows/tag.md) e [marque uma máquina virtual Linux em Azure](../virtual-machines/linux/tag.md).
1. Modifique os horários **Sequenciados-StartVM** e **Sequenced-StopVM** até à data e hora que satisfazem os seus requisitos e ativem o horário.
1. Execute o **livro de SequencedStartStop_Parent** com o parâmetro ACTION definido para **iniciar** e o parâmetro WHATIF definido para **True** para pré-visualizar as suas alterações.
1. Pré-visualizar a ação e fazer as alterações necessárias antes de implementar contra os VMs de produção. Quando estiver pronto, execute manualmente o livro de execução com o parâmetro definido para **Falso,** ou deixe que o programade Automação **Sequenced-StartVM** e **Sequenced-StopVM** corra automaticamente seguindo o horário prescrito.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Direcione a ação de início e paragem pela lista VM

1. Adicione um arranque de **sequência** e uma etiqueta de **sequência** stop com um valor inteiro positivo aos VMs que planeia adicionar ao parâmetro **VMList.**
1. Executar o **livro de** execução SequencedStartStop_Parent com o parâmetro ACTION definido para **iniciar,** adicione uma lista separada de VMs no parâmetro *VMList* e, em seguida, coloque o parâmetro WHATIF para **True**. Pré-visualizar as suas alterações.
1. Configure o parâmetro **External_ExcludeVMNames** com uma lista separada de VMs (VM1, VM2, VM3).
1. Este cenário não honra as variáveis **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupnames.** Para este cenário, é necessário criar o seu próprio horário de Automação. Para mais detalhes, consulte [Agendar um livro de execução em Automação Azure.](../automation/automation-schedules.md)
1. Pré-visualizar a ação e fazer as alterações necessárias antes de implementar contra os VMs de produção. Quando estiver pronto, execute manualmente o manual de monitorização e diagnóstico/monitorização-grupos de ação com o parâmetro definido para **Falso,** ou deixe que o horário de automatização **Sequenciado-StartVM** e **Sequenced-StopVM** corra automaticamente seguindo o horário prescrito.

## <a name="solution-components"></a>Componentes da solução

Esta solução inclui livros de execução, horários e integração pré-configurados com registos do Monitor Azure para que possa adaptar o arranque e o encerramento das suas máquinas virtuais de acordo com as suas necessidades de negócio.

### <a name="runbooks"></a>Runbooks

A tabela seguinte lista os livros de execução implantados na sua conta Automation por esta solução. Não faça alterações no código do livro de execução. Em vez disso, escreva o seu próprio livro para novas funcionalidades.

> [!IMPORTANT]
> Não execute diretamente nenhum livro de execução com "criança" anexado ao seu nome.

Todos os livros de execução dos pais incluem o parâmetro _WhatIf._ Quando definido para **True**, _o WhatIf_ suporta detalhar o comportamento exato que o livro de corridas toma quando executado sem o parâmetro _WhatIf_ e valida os VMs corretos estão a ser alvo. Um livro de execução só executa as suas ações definidas quando o parâmetro _WhatIf_ está definido para **Falso**.

|Runbook | Parâmetros | Descrição|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Ligou do livro dos pais. Este livro de execução cria alertas numa base por recurso para o cenário AutoStop.|
|AutoStop_CreateAlert_Parent | VMList<br> O que if: Verdadeiro ou Falso  | Cria ou atualiza as regras de alerta azure sobre VMs nos grupos de subscrição ou recursos direcionados. <br> VMList: Lista separada da Víramida de VMs. Por exemplo, _vm1, vm2, vm3_.<br> *O que If* valida a lógica do livro sem executar.|
|AutoStop_Disable | nenhuma | Desativa os alertas AutoStop e o horário predefinido.|
|AutoStop_StopVM_Child | WebHookData | Ligou do livro dos pais. As regras de alerta chamam este livro de corridas para parar o VM.|
|Bootstrap_Main | nenhuma | Usado uma vez para configurar configurações de bootstrap, como webhookURI, que normalmente não são acessíveis a partir do Azure Resource Manager. Este livro de execução é removido automaticamente após uma implementação bem sucedida.|
|ScheduledStartStop_Child | VMName <br> Ação: Iniciar ou Parar <br> ResourceGroupName | Ligou do livro dos pais. Executa uma ação de início ou paragem para a paragem programada.|
|ScheduledStartStop_Parent | Ação: Iniciar ou Parar <br>VMList <br> O que if: Verdadeiro ou Falso | Esta definição afeta todos os VMs na subscrição. Editar o **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames** apenas executar nestes grupos de recursos direcionados. Também pode excluir VMs específicos atualizando a **variável External_ExcludeVMNames.**<br> VMList: Lista separada da Víramida de VMs. Por exemplo, _vm1, vm2, vm3_.<br> _O que If_ valida a lógica do livro sem executar.|
|SequencedStartStop_Parent | Ação: Iniciar ou Parar <br> O que if: Verdadeiro ou Falso<br>VMList| Crie tags nomeadas **sequencestart** e **sequencestop** em cada VM para a qual pretende sequenciar a atividade de arranque/paragem. Estes nomes são sensíveis aos casos. O valor da etiqueta deve ser um inteiro positivo (1, 2, 3) que corresponda à ordem em que pretende iniciar ou parar. <br> VMList: Lista separada da Víramida de VMs. Por exemplo, _vm1, vm2, vm3_. <br> _O que If_ valida a lógica do livro sem executar. <br> **Nota:** Os VMs devem estar dentro de grupos de recursos definidos como External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames e External_ExcludeVMNames nas variáveis Azure Automation. Devem ter as etiquetas adequadas para que as ações possam produzir efeitos.|

### <a name="variables"></a>Variáveis

A tabela seguinte lista as variáveis criadas na sua conta De automação. Só modificar variáveis pré-fixas com **Externo**. Modificar variáveis pré-fixas com **efeitos internos** causa efeitos indesejáveis.

|Variável | Descrição|
|---------|------------|
|External_AutoStop_Condition | O operador condicional necessário para configurar a condição antes de desencadear um alerta. Os valores aceitáveis são **GreaterThan**, **GreaterThanEqual**, **LessThan**, e **LessThanOrEqual**.|
|External_AutoStop_Description | O alerta para parar o VM se a percentagem de CPU exceder o limiar.|
|External_AutoStop_MetricName | O nome da métrica de desempenho para a qual a regra de Alerta Azure deve ser configurado.|
|External_AutoStop_Threshold | O limiar da regra de alerta Azure especificado na _variável External_AutoStop_MetricName_. Os valores percentuais podem variar de 1 a 100.|
|External_AutoStop_TimeAggregationOperator | O operador de agregação de tempo, que é aplicado ao tamanho da janela selecionado para avaliar a circunstância. Os valores aceitáveis são **Médios,** **Mínimos,** **Máximo,** **Total**e **Último.**|
|External_AutoStop_TimeWindow | O tamanho da janela durante o qual Azure analisa métricas selecionadas para desencadear um alerta. Este parâmetro aceita a entrada em formato timepan. Os valores possíveis são de 5 minutos a 6 horas.|
|External_EnableClassicVMs| Especifica se os VMclássicos são visados pela solução. O valor predefinido é Verdadeiro. Isto deve ser definido como Falso para assinaturas De CSP. Os VMs clássicos requerem uma [conta clássica run-as](automation-create-standalone-account.md#classic-run-as-accounts).|
|External_ExcludeVMNames | Introduza nomes VM a excluir, separando nomes utilizando uma vírem sem espaços. Isto é limitado a 140 VMs. Se adicionar mais de 140 VMs a esta lista separada da vírpera, os VMs que estão definidos para serem excluídos podem ser inadvertidamente iniciados ou parados.|
|External_Start_ResourceGroupNames | Especifica um ou mais grupos de recursos, separando valores utilizando uma vírvia, direcionado para as ações de início.|
|External_Stop_ResourceGroupNames | Especifica um ou mais grupos de recursos, separando valores através de uma vírvia, direcionado para ações de paragem.|
|Internal_AutomationAccountName | Especifica o nome da conta Automation.|
|Internal_AutoSnooze_WebhookUri | Especifica Webhook URI chamado para o cenário DeParagem Automático.|
|Internal_AzureSubscriptionId | Especifica o ID de subscrição do Azure.|
|Internal_ResourceGroupName | Especifica o nome do grupo de recursos da conta Automation.|

Em todos os cenários, as **variáveis External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**e **External_ExcludeVMNames** são necessárias para direcionar as VMs, com exceção de fornecer uma lista separada de VMs para o **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**e **ScheduledStartStop_Parent** livros de execução. Ou seja, os seus VMs devem residir em grupos de recursos-alvo para que as ações de início e stop ocorram. A lógica funciona semelhante à política do Azure, na medida em que pode visar a subscrição ou grupo de recursos e ter ações herdadas por VMs recém-criados. Esta abordagem evita ter de manter um horário separado para cada VM e gerir partidas e paragens em escala.

### <a name="schedules"></a>Agendas

A tabela seguinte lista cada um dos horários predefinidos criados na sua conta Automation. Pode modificá-los ou criar os seus próprios horários personalizados. Por predefinição, todos os horários são desativados, exceto para **Scheduled_StartVM** e **Scheduled_StopVM**.

Não deve permitir todos os horários, porque isso pode criar ações de calendário sobrepostas. É melhor determinar quais as otimizações que pretende realizar e modificar em conformidade. Consulte os cenários de exemplo na secção de visão geral para obter mais explicações.

|Nome da agenda | Frequência | Descrição|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | A cada 8 horas | Executa o livro de AutoStop_CreateAlert_Parent a cada 8 horas, o que por sua vez para os valores baseados em VM em variáveis External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames e External_ExcludeVMNames nas variáveis Azure Automation. Em alternativa, pode especificar uma lista separada de VMs através do parâmetro VMList.|
|Scheduled_StopVM | Utilizador definido, diariamente | Executa o livro de Scheduled_Parent com um parâmetro de _stop_ todos os dias na hora especificada. Para automaticamente todos os VMs que cumprem as regras definidas pelas variáveis de ativos. Ativar o horário relacionado, **Programado-StartVM**.|
|Scheduled_StartVM | Utilizador definido, diariamente | Executa o Scheduled_Parent livro de corridas com um parâmetro de _início_ todos os dias na hora especificada. Inicia automaticamente todos os VMs que cumprem as regras definidas pelas variáveis apropriadas. Ativar o horário relacionado, **Paragem Programada-VM**.|
|Paragem sequenciada | 1:00 AM (UTC), todas as sextas-feiras | Executa o Sequenced_Parent livro de corridas com um parâmetro de _stop_ todas as sextas-feiras no momento especificado. Sequencialmente (ascendente) para todos os VMs com uma etiqueta de **SequenceStop** definida pelas variáveis apropriadas. Para obter mais informações sobre valores de etiqueta e variáveis de ativos, consulte a secção Runbooks. Ativar o horário relacionado, **Sequenciado-StartVM**.|
|Iniciado-Arranque-VM | 13:00 (UTC), todas as segundas-feiras | Executa o livro de Sequenced_Parent com um parâmetro de _início_ todas as segundas-feiras à hora especificada. Sequencialmente (descendente) inicia todos os VMs com uma etiqueta de **SequenceStart** definida pelas variáveis apropriadas. Para obter mais informações sobre valores de etiqueta e variáveis de ativos, consulte a secção Runbooks. Ativar o horário relacionado, **Sequenced-StopVM**.|

## <a name="azure-monitor-logs-records"></a>Registos de registos do Azure Monitor

A automação cria dois tipos de registos no espaço de trabalho do Log Analytics: registos de emprego e fluxos de trabalho.

### <a name="job-logs"></a>Registos de trabalhos

|Propriedade | Descrição|
|----------|----------|
|Autor da chamada |  Quem iniciou a operação. Os valores possíveis são um endereço de e-mail ou o sistema para trabalhos agendados.|
|Categoria | Classificação do tipo de dados. Para a Automatização, o valor é JobLogs.|
|CorrelationId | GUID que é a Id correlação do trabalho do livro de corridas.|
|JobId | GUID que é a identificação do trabalho do livro de corridas.|
|operationName | Especifica o tipo de operação efetuada no Azure. Para automação, o valor é Job.|
|resourceId | Especifica o tipo de recurso no Azure. Para a Automatização, o valor é a conta de Automatização associada ao runbook.|
|ResourceGroup | Especifica o nome do grupo de recursos do trabalho do runbook.|
|ResourceProvider | Especifica o serviço do Azure que fornece os recursos que pode implementar e gerir. Para a Automatização, o valor é Automatização do Azure.|
|ResourceType | Especifica o tipo de recurso no Azure. Para a Automatização, o valor é a conta de Automatização associada ao runbook.|
|resultType | O estado do trabalho do runbook. Os valores possíveis são:<br>- Iniciado<br>- Parado<br>- Suspenso<br>- Falhado<br>- Bem-sucedido|
|resultDescription | Descreve o estado do resultado do trabalho do runbook. Os valores possíveis são:<br>- Trabalho iniciado<br>- Trabalho falhado<br>- Trabalho Concluído|
|RunbookName | Especifica o nome do runbook.|
|SourceSystem | Especifica o sistema de origem dos dados submetidos. Para automação, o valor é OpsManager|
|StreamType | Especifica o tipo de evento. Os valores possíveis são:<br>- Verboso<br>- Saída<br>- Erro<br>- Aviso|
|SubscriptionId | Especifica o ID de subscrição do trabalho.
|Hora | Data e hora da execução do trabalho do runbook.|

### <a name="job-streams"></a>Fluxos de trabalho

|Propriedade | Descrição|
|----------|----------|
|Autor da chamada |  Quem iniciou a operação. Os valores possíveis são um endereço de e-mail ou o sistema para trabalhos agendados.|
|Categoria | Classificação do tipo de dados. Para a Automatização, o valor é JobStreams.|
|JobId | GUID que é a identificação do trabalho do livro de corridas.|
|operationName | Especifica o tipo de operação efetuada no Azure. Para automação, o valor é Job.|
|ResourceGroup | Especifica o nome do grupo de recursos do trabalho do runbook.|
|resourceId | Especifica o ID de recurso em Azure. Para a Automatização, o valor é a conta de Automatização associada ao runbook.|
|ResourceProvider | Especifica o serviço do Azure que fornece os recursos que pode implementar e gerir. Para a Automatização, o valor é Automatização do Azure.|
|ResourceType | Especifica o tipo de recurso no Azure. Para a Automatização, o valor é a conta de Automatização associada ao runbook.|
|resultType | O resultado do trabalho do runbook no momento em que o evento foi gerado. Um valor possível é:<br>- InProgress|
|resultDescription | Inclui o fluxo de saída do runbook.|
|RunbookName | O nome do runbook.|
|SourceSystem | Especifica o sistema de origem dos dados submetidos. Para a Automação, o valor é OpsManager.|
|StreamType | O tipo de fluxo de trabalho. Os valores possíveis são:<br>- Progresso<br>- Saída<br>- Aviso<br>- Erro<br>- Depuração<br>- Verboso|
|Hora | Data e hora da execução do trabalho do runbook.|

Quando efetua qualquer pesquisa de registo que retorne os registos da categoria de **JobLogs** ou **JobStreams,** pode selecionar a vista **JobLogs** ou **JobStreams,** que apresenta um conjunto de azulejos resumindo as atualizações devolvidas pela pesquisa.

## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo

A tabela seguinte disponibiliza pesquisas de registos de exemplo para registos de trabalhos que esta solução recolhe.

|Consulta | Descrição|
|----------|----------|
|Encontre empregos para ScheduledStartStop_Parent runbook que terminaram com sucesso | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Encontre empregos para SequencedStartStop_Parent runbook que terminaram com sucesso | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="viewing-the-solution"></a>Visualizar a solução

Para aceder à solução, navegue para a sua Conta de Automação, selecione **Workspace** em **RECURSOS RELACIONADOS**. Na página de análise de registo, selecione **Soluções** em **GERAL**. Na página **Soluções,** selecione a solução **Start-Stop-VM[workspace]** da lista.

A seleção da solução mostra a página de solução **Start-Stop-VM[workspace].** Aqui pode rever detalhes importantes, como o azulejo **StartStopVM.** Tal como no seu espaço de trabalho log analytics, este azulejo apresenta uma contagem e uma representação gráfica dos trabalhos do livro de corridas para a solução que começou e terminou com sucesso.

![Página de soluções de gestão de atualização de automatização](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

A partir daqui, pode efetuar uma análise mais aprofundada dos registos de trabalho clicando no azulejo dodo. O dashboard de solução mostra histórico de trabalho e consultas de pesquisa de registo pré-definidas. Mude para o portal avançado de análise de registos para pesquisar com base nas suas consultas de pesquisa.

## <a name="configure-email-notifications"></a>Configurar as notificações por e-mail

Para alterar as notificações de e-mail após a implementação da solução, modifique o grupo de ação que foi criado durante a implementação.  

> [!NOTE]
> As subscrições na Nuvem do Governo Azure não suportam a funcionalidade de e-mail desta solução.

No portal Azure, navegue para monitor -> Grupos de ação. Selecione o grupo de ação intitulado **StartStop_VM_Notication**.

![Página de soluções de gestão de atualização de automatização](media/automation-solution-vm-management/azure-monitor.png)

Na página **StartStop_VM_Notification,** clique em **Editar detalhes** em **Detalhes**. Isto abre a página **email/SMS/Push/Voice.** Atualize o endereço de e-mail e clique **em OK** para guardar as suas alterações.

![Página de soluções de gestão de atualização de automatização](media/automation-solution-vm-management/change-email.png)

Alternativamente, você pode adicionar ações adicionais ao grupo de ação, para saber mais sobre grupos de ação, ver [grupos de ação](../azure-monitor/platform/action-groups.md)

Segue-se um e-mail de exemplo que é enviado quando a solução desliga as máquinas virtuais.

![Página de soluções de gestão de atualização de automatização](media/automation-solution-vm-management/email.png)

## <a name="add-exclude-vms"></a>Adicionar/Excluir VMs

A solução fornece a capacidade de adicionar VMs a serem visados pela solução ou especificamente excluir máquinas da solução.

### <a name="add-a-vm"></a>Adicione um VM

Existem algumas opções que pode utilizar para se certificar de que um VM está incluído na solução Iniciar/Parar quando funciona.

* Cada um dos [livros-mãe](#runbooks) da solução tem um parâmetro **VMList.** Pode passar uma lista separada de nomes vm separados para este parâmetro ao agendar o livro de execução dos pais apropriado para a sua situação e estes VMs serão incluídos quando a solução funcionar.

* Para selecionar vários VMs, detete o **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames** com os nomes do grupo de recursos que contêm os VMs que pretende iniciar ou parar. Também pode definir este valor para `*`, para que a solução seja executada contra todos os grupos de recursos na subscrição.

### <a name="exclude-a-vm"></a>Excluir um VM

Para excluir um VM da solução, pode adicioná-lo à **variável External_ExcludeVMNames.** Esta variável é uma lista separada de VMs específicos para excluir da solução Iniciar/Parar. Esta lista está limitada a 140 VMs. Se adicionar mais de 140 VMs a esta lista separada da vírpera, os VMs que estão definidos para serem excluídos podem ser inadvertidamente iniciados ou parados.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Modificar os horários de arranque e encerramento

Gerir os horários de arranque e encerramento nesta solução segue os mesmos passos que delineados no Agendamento de um livro de [execução em Automação Azure.](automation-schedules.md) Tem de haver um horário separado para começar e parar os VMs.

É suportado configurar a solução para parar os VMs num determinado momento. Neste cenário basta criar **um** cronograma e nenhum **início** correspondente agendado. Para efetuar este procedimento, tem de:

1. Certifique-se de que adicionou os grupos de recursos para que os VMs desligassem na **variável External_Stop_ResourceGroupNames.**
2. Crie o seu próprio horário para o tempo que pretende desligar os VMs.
3. Navegue para o **livro de ScheduledStartStop_Parent** e clique em **Agendar**. Isto permite-lhe selecionar o horário que criou no passo anterior.
4. **Selecione Parâmetros e executar definições** e defina o parâmetro ACTION para "Stop".
5. Clique em **OK** para guardar as alterações.

## <a name="update-the-solution"></a>Atualizar a solução

Se implementou uma versão anterior desta solução, tem primeiro de a apagar da sua conta antes de implementar uma versão atualizada. Siga os passos para [remover a solução](#remove-the-solution) e, em seguida, siga os passos acima para [implementar a solução](#deploy-the-solution).

## <a name="remove-the-solution"></a>Remover a solução

Se decidir que já não precisa de utilizar a solução, pode eliminá-la da conta Automation. A eliminação da solução apenas remove os livros de execução. Não apaga os horários ou variáveis que foram criados quando a solução foi adicionada. Esses ativos que precisa de apagar manualmente se não estiver a usá-los com outros livros de execução.

Para eliminar a solução, execute os seguintes passos:

1. Na sua conta de Automação, sob **recursos Relacionados,** selecione espaço de **trabalho Linked**.
1. Selecione **Ir para o espaço de trabalho**.
1. Em **Geral,** selecione **Soluções**. 
1. Na página **Soluções,** selecione a solução **Start-Stop-VM[Workspace]** . Na página **VMManagementSolution[Workspace],** a partir do menu, **selecione Delete**.<br><br> ![eliminar](media/automation-solution-vm-management/vm-management-solution-delete.png) de solução VM Mgmt
1. Na janela **Eliminar Solução,** confirme se pretende eliminar a solução.
1. Enquanto a informação é verificada e a solução é eliminada, pode acompanhar o seu progresso no âmbito de **Notificações** do menu. É devolvido à página **Soluções** após o início do processo para remover a solução.

A conta de Automação e o espaço de trabalho log Analytics não são eliminados como parte deste processo. Se não quiser reter o espaço de trabalho do Log Analytics, tem de o eliminar manualmente. Isto pode ser realizado a partir do portal Azure:

1. No portal Azure, procure e selecione espaços de **trabalho Log Analytics**.
1. Na página dos espaços de **trabalho Log Analytics,** selecione o espaço de trabalho.
1. **Selecione Eliminar** a partir do menu na página de definições do espaço de trabalho.

Se não quiser reter os componentes da conta Azure Automation, pode eliminar manualmente cada um deles. Para a lista de livros de execução, variáveis e horários criados pela solução, consulte os componentes da [Solução.](#solution-components)

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre como construir diferentes consultas de pesquisa e rever os registos de trabalho da Automation com registos do Monitor Azure, consulte as pesquisas de [registo em registos do Monitor Azure](../log-analytics/log-analytics-log-searches.md).
- Para saber mais sobre a execução de runbooks, como monitorizar tarefas de runbooks e outros detalhes técnicos, veja [Acompanhar uma tarefa de runbook](automation-runbook-execution.md).
- Para saber mais sobre os registos do Monitor Do Azure e as fontes de recolha de dados, consulte a Recolha de dados de [armazenamento do Azure em toda a visão geral dos registos do Monitor Do Azure.](../azure-monitor/platform/collect-azure-metrics-logs.md)
