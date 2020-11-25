---
title: Gerir permissões de funções e segurança na Azure Automation
description: Este artigo descreve como usar o controlo de acesso baseado em funções Azure (Azure RBAC), que permite a gestão de acesso para recursos Azure.
keywords: rbac de automatização, controlo de acesso baseado em funções , rbac do azure
services: automation
ms.subservice: shared-capabilities
ms.date: 07/21/2020
ms.topic: conceptual
ms.openlocfilehash: efdb195ad41b036f7f470884b3a441de1db7f7f4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003706"
---
# <a name="manage-role-permissions-and-security"></a>Gerir permissões e segurança de funções

O controlo de acesso baseado em funções Azure (Azure RBAC) permite a gestão do acesso aos recursos Azure. Utilizando [o Azure RBAC,](../role-based-access-control/overview.md)pode segregar os deveres dentro da sua equipa e conceder apenas a quantidade de acesso aos utilizadores, grupos e aplicações de que necessitam para desempenharem os seus trabalhos. Pode conceder acesso baseado em funções aos utilizadores utilizando o portal Azure, ferramentas de Command-Line Azure ou APIs de Gestão Azure.

## <a name="roles-in-automation-accounts"></a>Funções em contas de Automação

Na Azure Automation, o acesso é concedido atribuindo o papel Azure adequado aos utilizadores, grupos e aplicações no âmbito da conta Automation. Seguem-se as funções incorporadas suportadas por uma conta de Automatização:

| **Role** | **Descrição** |
|:--- |:--- |
| Proprietário |A função Proprietário permite o acesso a todos os recursos e ações dentro de uma conta de Automação, incluindo o acesso a outros utilizadores, grupos e aplicações para gerir a conta Automation. |
| Contribuinte |A função de Contribuidor permite-lhe gerir tudo, exceto modificar as permissões de acesso de outro utilizador para uma conta de Automatização. |
| Leitor |A função de Leitor permite-lhe ver todos os recursos numa conta de Automatização, mas não permite efetuar quaisquer alterações. |
| Operador de Automatização |A função de Operador de Automação permite-lhe visualizar o nome e propriedades do runbook e criar e gerir empregos para todos os runbooks numa conta de Automação. Esta função é útil se quiser proteger os recursos da sua conta de Automação, como ativos de credenciais e livros de execução, de serem vistos ou modificados, mas ainda assim permitir que os membros da sua organização executem estes runbooks. |
|Operador de emprego de automação|A função de Operador de Emprego da Automação permite-lhe criar e gerir postos de trabalho para todos os livros numa conta de Automação.|
|Operador de runbook de automação|A função de Operador de Runbook Automation permite-lhe visualizar o nome e propriedades de um livro de contas.|
| Contribuidor do Log Analytics | A função De Colaborador do Log Analytics permite-lhe ler todos os dados de monitorização e editar as definições de monitorização. As definições de monitorização de edição incluem a adição da extensão VM aos VMs, a leitura das chaves da conta de armazenamento para poder configurar a recolha de registos a partir do armazenamento da Azure, criar e configurar contas de Automação, adicionar funcionalidades de Automação Azure e configurar diagnósticos Azure em todos os recursos Azure.|
| Leitor do Log Analytics | A função 'Leitor de Análise de Registo' permite-lhe visualizar e pesquisar todos os dados de monitorização, bem como visualizar as definições de monitorização. Isto inclui visualizar a configuração dos diagnósticos Azure em todos os recursos Azure. |
| Colaborador de monitorização | A função de Colaborador de Monitorização permite-lhe ler todos os dados de monitorização e atualizar as definições de monitorização.|
| Leitor de Monitorização | A função Monitoring Reader permite-lhe ler todos os dados de monitorização. |
| Administrador de Acesso dos Utilizadores |A função de Administrador de Acesso de Utilizador permite-lhe gerir o acesso de utilizador para as contas de Automatização do Azure. |

## <a name="role-permissions"></a>Permissões de funções

As tabelas que se seguem descrevem as permissões específicas dadas a cada papel. Isto pode incluir Ações, que dão permissões, e NotActions, que as restringem.

### <a name="owner"></a>Proprietário

Um Proprietário pode gerir tudo, incluindo acesso. A tabela a seguir mostra as permissões concedidas para o papel:

|Ações|Descrição|
|---|---|
|Microsoft.Automation/automation|Criar e gerir recursos de todos os tipos.|

### <a name="contributor"></a>Contribuinte

Um Contribuinte pode gerir tudo, exceto o acesso. A tabela a seguir mostra as permissões concedidas e negadas para o papel:

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Automation/automation|Criar e gerir recursos de todos os tipos|
|**Não ações**||
|Microsoft.Authorization/*/Delete| Eliminar papéis e atribuições de funções.       |
|Microsoft.Authorization/*/Write     |  Criar papéis e atribuições de papéis.       |
|Microsoft.Authorization/elevateAccess/Action    | Nega a capacidade de criar um Administrador de Acesso ao Utilizador.       |

### <a name="reader"></a>Leitor

Um Leitor pode ver todos os recursos numa conta Demôm automação, mas não pode fazer quaisquer alterações.

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Automation/automationAs contas/leitura|Ver todos os recursos numa conta De Automação. |

### <a name="automation-operator"></a>Operador de Automatização

Um Operador de Automação é capaz de criar e gerir postos de trabalho, e ler nomes e propriedades de runbooks para todos os runbooks numa conta de Automação.

>[!NOTE]
>Se pretende controlar o acesso do operador a livros individuais, então não desista desta função. Em vez disso, utilize as funções de **Operador de Trabalho de Automação** e **Demóm automação em** combinação.

A tabela a seguir mostra as permissões concedidas para o papel:

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Autorização/*/ler|Leia a autorização.|
|Microsoft.Automation/automationAs/hybridRunbookWorkerGroups/read|Leia os recursos operários híbridos runbook.|
|Microsoft.Automation/automationAs contas/empregos/leitura|Lista de trabalhos do livro de corridas.|
|Microsoft.Automation/automationAs/empregos/currículo/ação|Retomar um trabalho que está parado.|
|Microsoft.Automation/automationAs contas/empregos/stop/action|Cancele um trabalho em andamento.|
|Microsoft.Automation/automationContagens/empregos/streams/read|Leia os Fluxos de Trabalho e Saída.|
|Microsoft.Automation/automationAs contas/empregos/output/read|Obter a saída de um emprego.|
|Microsoft.Automation/automationAs/empregos/suspensão/ação|Faça uma pausa num trabalho em progresso.|
|Microsoft.Automation/automationAs contas/empregos/escrita|Criar empregos.|
|Microsoft.Automation/automationAs contas/trabalhoSchedules/read|Obtenha um horário de trabalho da Azure Automation.|
|Microsoft.Automation/automationAs/trabalhoSchedules/write|Crie um horário de trabalho da Azure Automation.|
|Microsoft.Automation/automationAs contas/linkedWorkspace/read|Ligar o espaço de trabalho à conta Automation.|
|Microsoft.Automation/automationAs contas/leitura|Obtenha uma conta da Azure Automation.|
|Microsoft.Automation/automationAs contas/runbooks/read|Obtenha um livro de bordo da Azure Automation.|
|Microsoft.Automation/automationContagens/horários/leitura|Obtenha um ativo de agendação Azure Automation.|
|Microsoft.Automation/automationContagens/horários/escrita|Criar ou atualizar um ativo de agendação Azure Automation.|
|Microsoft.Resources/subscrições/resourceGroups/read      |Leia papéis e atribuições de papéis.         |
|Microsoft.Resources/implementações/*      |Criar e gerir implementações de grupos de recursos.         |
|Microsoft.Insights/alertRules/*      | Criar e gerir regras de alerta.        |
|Microsoft.Support/* |Criar e gerir bilhetes de apoio.|

### <a name="automation-job-operator"></a>Operador de emprego de automação

Uma função de Operador de Emprego da Automação é concedida no âmbito da conta Automation.Isto permite ao operador permissões para criar e gerir postos de trabalho para todos os livros na conta. Se a função de Operador de Emprego for concedida, leia permissões de leitura no grupo de recursos que contém a conta Automation, os membros da função têm a capacidade de iniciar livros de execução. No entanto, não têm a capacidade de criar, editar ou apagar.

A tabela a seguir mostra as permissões concedidas para o papel:

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Autorização/*/ler|Leia a autorização.|
|Microsoft.Automation/automationAs contas/empregos/leitura|Lista de trabalhos do livro de corridas.|
|Microsoft.Automation/automationAs/empregos/currículo/ação|Retomar um trabalho que está parado.|
|Microsoft.Automation/automationAs contas/empregos/stop/action|Cancele um trabalho em andamento.|
|Microsoft.Automation/automationContagens/empregos/streams/read|Leia os Fluxos de Trabalho e Saída.|
|Microsoft.Automation/automationAs/empregos/suspensão/ação|Faça uma pausa num trabalho em progresso.|
|Microsoft.Automation/automationAs contas/empregos/escrita|Criar empregos.|
|Microsoft.Resources/subscrições/resourceGroups/read      |  Leia papéis e atribuições de papéis.       |
|Microsoft.Resources/implementações/*      |Criar e gerir implementações de grupos de recursos.         |
|Microsoft.Insights/alertRules/*      | Criar e gerir regras de alerta.        |
|Microsoft.Support/* |Criar e gerir bilhetes de apoio.|

### <a name="automation-runbook-operator"></a>Operador de runbook de automação

Uma função de operador de runbook de automação é concedida no âmbito runbook. Um operador de runbook automation pode ver o nome e propriedades do livro de bordo.Esta função combinada com a função **de Operador de Emprego da Automação** permite ao operador também criar e gerir postos de trabalho para o livro de corridas. A tabela a seguir mostra as permissões concedidas para o papel:

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Automation/automationAs contas/runbooks/read     | Listar os livros.        |
|Microsoft.Autorização/*/ler      | Leia a autorização.        |
|Microsoft.Resources/subscrições/resourceGroups/read      |Leia papéis e atribuições de papéis.         |
|Microsoft.Resources/implementações/*      | Criar e gerir implementações de grupos de recursos.         |
|Microsoft.Insights/alertRules/*      | Criar e gerir regras de alerta.        |
|Microsoft.Support/*      | Criar e gerir bilhetes de apoio.        |

### <a name="log-analytics-contributor"></a>Contribuidor do Log Analytics

Um Colaborador Do Log Analytics pode ler todos os dados de monitorização e editar as definições de monitorização. As definições de monitorização de edição incluem a adição da extensão VM aos VM; leitura das chaves da conta de armazenamento para poder configurar a recolha de registos do Azure Storage; criar e configurar contas de automação; adição de funcionalidades; e configurar diagnósticos Azure em todos os recursos da Azure. A tabela a seguir mostra as permissões concedidas para o papel:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/ler|Leia recursos de todos os tipos, exceto segredos.|
|Microsoft.Automation/automation|Gerir contas de automação.|
|Microsoft.ClassicCompute/virtualMachines/extensões/*|Criar e gerir extensões de máquinas virtuais.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Lista chaves clássicas de conta de armazenamento.|
|Microsoft.Compute/virtualMachines/extensões/*|Crie e gere extensões de máquinas virtuais clássicas.|
|Microsoft.Insights/alertRules/*|Leia/escreva/apague as regras de alerta.|
|Microsoft.Insights/diagnosticSettings/*|Ler/escrever/apagar definições de diagnóstico.|
|Microsoft.OperationalInsights/*|Gerir registos do Monitor Azure.|
|Microsoft.OperationsManagement/*|Gerir funcionalidades da Azure Automation em espaços de trabalho.|
|Microsoft.Resources/implementações/*|Criar e gerir implementações de grupos de recursos.|
|Microsoft.Resources/subscrições/grupos de recursos/implementações/*|Criar e gerir implementações de grupos de recursos.|
|Microsoft.Storage/storageAcontas/listKeys/ação|Listar as chaves da conta de armazenamento.|
|Microsoft.Support/*|Criar e gerir bilhetes de apoio.|

### <a name="log-analytics-reader"></a>Leitor do Log Analytics

Um Leitor de Analítica de Log pode visualizar e pesquisar todos os dados de monitorização, bem como visualizar as definições de monitorização, incluindo visualizar a configuração de diagnósticos Azure em todos os recursos Azure. A tabela a seguir mostra as permissões concedidas ou negadas para o papel:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/ler|Leia recursos de todos os tipos, exceto segredos.|
|Microsoft.OperationalInsights/workspaces/analytics/consulta/ação|Gerir consultas em registos do Monitor Azure.|
|Microsoft.OperationalInsights/workspaces/search/action|Procure os dados de registo do Azure Monitor.|
|Microsoft.Support/*|Criar e gerir bilhetes de apoio.|
|**Não ações**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Não consigo ler as chaves de acesso partilhadas.|

### <a name="monitoring-contributor"></a>Colaborador de monitorização

Um Colaborador de Monitorização pode ler todos os dados de monitorização e atualizar as definições de monitorização. A tabela a seguir mostra as permissões concedidas para o papel:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/ler|Leia recursos de todos os tipos, exceto segredos.|
|Microsoft.AlertsManagement/alerts/*|Gerir alertas.|
|Microsoft.AlertsManagement/alertsSummary/*|Gerir o painel de alerta.|
|Microsoft.Insights/AlertRules/*|Gerir as regras de alerta.|
|Microsoft.Insights/componentes/*|Gerir os componentes de Insights de Aplicação.|
|Microsoft.Insights/DiagnosticSettings/*|Gerir as definições de diagnóstico.|
|Microsoft.Insights/eventtypes/*|Liste eventos de Registo de Atividade (eventos de gestão) numa subscrição. Esta permissão aplica-se tanto ao acesso programático como ao portal ao Registo de Atividades.|
|Microsoft.Insights/LogDefinitions/*|Esta permissão é necessária para os utilizadores que necessitem de acesso aos Registos de Atividade através do portal. Listar categorias de registo em Registo de Atividade.|
|Microsoft.Insights/MetricDefinitions/*|Leia definições métricas (lista de tipos métricos disponíveis para um recurso).|
|Microsoft.Insights/Metrics/*|Leia as métricas para um recurso.|
|Microsoft.Insights/Register/Action|Registe-se no fornecedor Microsoft.Insights.|
|Microsoft.Insights/webtests/*|Gerir testes web de Insights de Aplicação.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Gerir os pacotes de soluções de registos do Azure Monitor.|
|Microsoft.OperationalInsights/workspaces/saveSearches/*|Gerencie os registos do Azure Monitor, guarde as pesquisas.|
|Microsoft.OperationalInsights/workspaces/search/action|Pesquisar log analytics espaços de trabalho.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Listar as chaves para um espaço de trabalho Log Analytics.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Gerir as configurações de insight de armazenamento do Monitor Azure.|
|Microsoft.Support/*|Criar e gerir bilhetes de apoio.|
|Microsoft.WorkloadMonitor/workloads/*|Gerir cargas de trabalho.|

### <a name="monitoring-reader"></a>Leitor de Monitorização

Um Leitor de Monitorização pode ler todos os dados de monitorização. A tabela a seguir mostra as permissões concedidas para o papel:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/ler|Leia recursos de todos os tipos, exceto segredos.|
|Microsoft.OperationalInsights/workspaces/search/action|Pesquisar log analytics espaços de trabalho.|
|Microsoft.Support/*|Criar e gerir bilhetes de apoio|

### <a name="user-access-administrator"></a>Administrador de Acesso dos Utilizadores

Um Administrador de Acesso ao Utilizador pode gerir o acesso do utilizador aos recursos do Azure. A tabela a seguir mostra as permissões concedidas para o papel:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/ler|Ler todos os recursos|
|Microsoft.Authorization/*|Gerir a autorização|
|Microsoft.Support/*|Criar e gerir bilhetes de apoio|

## <a name="feature-setup-permissions"></a>Permissões de configuração de recursos

As seguintes secções descrevem as permissões mínimas necessárias para permitir as funcionalidades de Gestão de Atualização e Descodamento e Inventário de Alterações.

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-a-vm"></a>Permissões para permitir a gestão de atualização e alterar o rastreio e o inventário a partir de um VM

|**Ação**  |**Permissão**  |**Âmbito mínimo**  |
|---------|---------|---------|
|Escrever nova implantação      | Microsoft.Resources/implementações/*          |Subscrição          |
|Escreva novo grupo de recursos      | Microsoft.Resources/subscrições/resourceGroups/write        | Subscrição          |
|Criar novo espaço de trabalho predefinido      | Microsoft.OperationalInsights/workspaces/write         | Grupo de recursos         |
|Criar nova Conta      |  Microsoft.Automation/automationAs contas/escrita        |Grupo de recursos         |
|Link espaço de trabalho e conta      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAs contas/leitura|Área de trabalho</br>Conta de automatização
|Criar extensão de MMA      | Microsoft.Compute/virtualMachines/write         | Máquina Virtual         |
|Criar pesquisa guardada      | Microsoft.OperationalInsights/workspaces/write          | Área de trabalho         |
|Criar config de âmbito      | Microsoft.OperationalInsights/workspaces/write          | Área de trabalho         |
|Verificação do estado de embarque - Ler espaço de trabalho      | Microsoft.OperationalInsights/workspaces/read         | Área de trabalho         |
|Verificação do estado de embarque - Ler propriedade ligada ao espaço de trabalho da conta     | Microsoft.Automation/automationAs contas/leitura      | Conta de automatização        |
|Verificação do estado de embarque - Ler solução      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Solução         |
|Verificação do estado de embarque - Ler VM      | Microsoft.Compute/virtualMachines/ler         | Máquina Virtual         |
|Verificação do estado de embarque - Ler conta      | Microsoft.Automation/automationAs contas/leitura  |  Conta de automatização   |
| Verificação do espaço de trabalho no embarque para VM<sup>1</sup>       | Microsoft.OperationalInsights/workspaces/read         | Subscrição         |
| Registe-se o fornecedor De Registos |Microsoft.Insights/register/action | Subscrição|

<sup>1</sup> Esta permissão é necessária para permitir funcionalidades através da experiência do portal VM.

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-an-automation-account"></a>Permissões para permitir a gestão de atualização e alterar o rastreio e o inventário a partir de uma conta de Automação

|**Ação**  |**Permissão** |**Âmbito mínimo**  |
|---------|---------|---------|
|Criar nova implantação     | Microsoft.Resources/implementações/*        | Subscrição         |
|Criar novo grupo de recursos     | Microsoft.Resources/subscrições/resourceGroups/write         | Subscrição        |
|AutomationOnboarding blade - Criar novo espaço de trabalho     |Microsoft.OperationalInsights/workspaces/write           | Grupo de recursos        |
|AutomationOnboarding blade - leia o espaço de trabalho ligado     | Microsoft.Automation/automationAs contas/leitura        | Conta de automatização       |
|AutomationOnboarding blade - leia solução     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Solução        |
|AutomationOnboarding blade - leia o espaço de trabalho     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Área de trabalho        |
|Criar link para espaço de trabalho e Conta     | Microsoft.OperationalInsights/workspaces/write        | Área de trabalho        |
|Escrever conta para caixa de sapatos      | Microsoft.Automation/automationAs contas/escrita        | Conta        |
|Criar/editar pesquisa guardada     | Microsoft.OperationalInsights/workspaces/write        | Área de trabalho        |
|Criar/editar âmbito config     | Microsoft.OperationalInsights/workspaces/write        | Área de trabalho        |
| Registe-se o fornecedor De Registos |Microsoft.Insights/register/action | Subscrição|
|**Passo 2 - Ativar vários VMs**     |         |         |
|VMOnboarding blade - Criar extensão MMA     | Microsoft.Compute/virtualMachines/write           | Máquina Virtual        |
|Criar /editar pesquisa guardada     | Microsoft.OperationalInsights/workspaces/write           | Área de trabalho        |
|Criar /editar âmbito config  | Microsoft.OperationalInsights/workspaces/write   | Área de trabalho|

## <a name="update-management-permissions"></a>Atualizar permissões de gestão

A gestão de atualização chega a vários serviços para prestar o seu serviço. A tabela a seguir mostra as permissões necessárias para gerir as implementações de gestão de atualização:

|**Recurso**  |**Role**  |**Âmbito**  |
|---------|---------|---------|
|Conta de automatização     | Contribuidor do Log Analytics       | Conta de automatização        |
|Conta de automatização    | Contribuidor de Máquina Virtual        | Grupo de Recursos para a conta        |
|Área de trabalho do Log Analytics     | Contribuidor do Log Analytics| Área de trabalho do Log Analytics        |
|Área de trabalho do Log Analytics |Leitor do Log Analytics| Subscrição|
|Solução     |Contribuidor do Log Analytics         | Solução|
|Máquina Virtual     | Contribuidor de Máquina Virtual        | Máquina Virtual        |

## <a name="configure-azure-rbac-for-your-automation-account"></a>Configure Azure RBAC para a sua conta de Automação

A seguinte secção mostra-lhe como configurar o Azure RBAC na sua conta de Automação através do [portal Azure](#configure-azure-rbac-using-the-azure-portal) e [powerShell](#configure-azure-rbac-using-powershell).

### <a name="configure-azure-rbac-using-the-azure-portal"></a>Configurar a Azure RBAC usando o portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) e abra a sua conta de Automatização a partir da página Contas de Automatização.
2. Clique no **controlo de acesso (IAM)** para abrir a página de Controlo de Acesso (IAM). Pode utilizar esta página para adicionar novos utilizadores, grupos e aplicações para gerir a sua conta Demôm automação e ver as funções existentes que são configuráveis para a conta Automation.
3. Clique no separador **Atribuições de funções**.

   ![Botão de acesso](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Adicionar um novo utilizador e atribuir uma função

1. A partir da página de Controlo de Acesso (IAM), clique **+ Adicionar a atribuição de função**. Esta ação abre a página de atribuição de funções Add onde pode adicionar um utilizador, grupo ou aplicação e atribuir uma função correspondente.

2. Selecione uma função na lista de funções disponíveis. Pode escolher qualquer uma das funções incorporadas disponíveis que uma conta Automation suporta ou qualquer função personalizada que possa ter definido.

3. Digite o nome do utilizador a que pretende dar permissões no campo **Select.** Escolha o utilizador na lista e clique em **Guardar.**

   ![Adicionar utilizadores](media/automation-role-based-access-control/automation-04-add-users.png)

   Agora deve ver o utilizador adicionado à página do Utilizador, com a função selecionada atribuída.

   ![Listar utilizadores](media/automation-role-based-access-control/automation-05-list-users.png)

   Também pode atribuir uma função ao utilizador a partir da página Funções.

4. Clique **em Funções** a partir da página 'Controlo de Acesso' (IAM) para abrir a página Roles. Pode ver o nome da função e o número de utilizadores e grupos atribuídos a essa função.

    ![Atribuir função a partir da página Utilizadores](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Só é possível definir o controlo de acesso baseado em funções no âmbito da conta Automation e não em qualquer recurso abaixo da conta Automation.

#### <a name="remove-a-user"></a>Remover um utilizador

Pode remover a permissão de acesso para um utilizador que não esteja a gerir a conta Automation, ou que já não trabalhe para a organização. Seguem-se os passos para remover um utilizador:

1. A partir da página 'Controlo de Acesso' (IAM), selecione o utilizador para remover e clique em **Remover**.
2. Clique no botão **Remover** na página de detalhes de atribuição.
3. Clique em **Sim** para confirmar a remoção.

   ![Remover utilizadores](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-azure-rbac-using-powershell"></a>Configure Azure RBAC usando PowerShell

Também pode configurar o acesso baseado em funções a uma conta Automation utilizando os [seguintes cmdlets Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

[A Get-AzRoleDefinition](/powershell/module/Az.Resources/Get-AzRoleDefinition?view=azps-3.7.0) lista todas as funções Azure que estão disponíveis no Azure Ative Directory. Pode utilizar este cmdlet com o `Name` parâmetro para listar todas as ações que um papel específico pode desempenhar.

```azurepowershell-interactive
Get-AzRoleDefinition -Name 'Automation Operator'
```

Segue-se a saída de exemplo:

```azurepowershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action,
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
```

[A Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment?view=azps-3.7.0) lista atribuições de funções Azure no âmbito especificado. Sem parâmetros, este cmdlet devolve todas as atribuições de funções es feitas no âmbito da subscrição. Utilize o `ExpandPrincipalGroups` parâmetro para listar as atribuições de acesso para o utilizador especificado, bem como os grupos a que o utilizador pertence.

**Exemplo:** Utilize o cmdlet seguinte para listar todos os utilizadores e as suas funções numa conta Automation.

```azurepowershell-interactive
Get-AzRoleAssignment -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Segue-se a saída de exemplo:

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

Utilize [o Design New-AzRoleAss para](/powershell/module/Az.Resources/New-AzRoleAssignment?view=azps-3.7.0) atribuir acesso a utilizadores, grupos e aplicações a um determinado âmbito.

**Exemplo:** Utilize o seguinte comando para atribuir a função "Operador de Automação" a um utilizador no âmbito da conta Automação.

```azurepowershell-interactive
New-AzRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Segue-se a saída de exemplo:

```azurepowershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

Utilize [o Remove-AzRoleAssignment](/powershell/module/Az.Resources/Remove-AzRoleAssignment?view=azps-3.7.0) para remover o acesso de um utilizador, grupo ou aplicação especificados de um determinado âmbito.

**Exemplo:** Utilize o seguinte comando para remover o utilizador da função de Operador de Automação no âmbito da conta Automação.

```azurepowershell-interactive
Remove-AzRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

No exemplo anterior, `sign-in ID of a user you wish to remove` `SubscriptionID` substitua, e pelos detalhes da sua `Resource Group Name` `Automation account name` conta. Escolha **sim** quando solicitado para confirmar antes de continuar a remover as atribuições de funções do utilizador.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Experiência do utilizador para papel de Operador de Automação - Conta de automação

Quando um utilizador atribuído à função de Operador de Automação no âmbito da conta Automation vê a conta Automation a que é atribuída, o utilizador só pode visualizar a lista de runbooks, trabalhos de runbook e horários criados na conta Automation. Este utilizador não pode visualizar as definições destes itens. O utilizador pode iniciar, parar, suspender, retomar ou agendar o trabalho de runbook. No entanto, o utilizador não tem acesso a outros recursos de Automação, tais como configurações, grupos de trabalhadores híbridos ou nós DSC.

![Sem acesso a recursos](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-azure-rbac-for-runbooks"></a>Configurar Azure RBAC para runbooks

A Azure Automation permite-lhe atribuir funções Azure a livros de execução específicos. Para isso, execute o seguinte script para adicionar um utilizador a um livro de aplicação específico. Um Administrador de Conta de Automação ou um Administrador inquilino pode executar este script.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation account
$automationAccountName ="<Automation account name>" # Name of the Automation account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation account resource
$aa = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$rbName"

# The Automation Job Operator role only needs to be run once per user.
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

Uma vez executado o script, faça o login do utilizador no portal Azure e selecione **Todos os Recursos**. Na lista, o utilizador pode ver o livro de execução para o qual foi adicionado como Operador de Runbook Automation.

![Runbook Azure RBAC no portal](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Experiência do utilizador para papel de operador de automação - Runbook

Quando um utilizador atribuído à função de Operador de Automação no âmbito Runbook vê um livro de execução atribuído, o utilizador só pode iniciar o livro de aplicação e ver os trabalhos de runbook.

![Só tem acesso para começar](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o Azure RBAC usando PowerShell, consulte [adicionar ou remover atribuições de funções Azure usando Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Para obter mais informações sobre os tipos de livros de execução, consulte os tipos de [runbook da Azure Automation](automation-runbook-types.md).
* Para iniciar um livro de bordo, consulte [Iniciar um livro de recortes na Azure Automation](start-runbooks.md).
