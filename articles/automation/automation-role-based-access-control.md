---
title: Controlo de acesso baseado em funções na Automatização do Azure
description: O controlo de acesso baseado em funções (RBAC) permite uma gestão de acesso para os recursos do Azure. Este artigo descreve como configurar o RBAC na Automatização do Azure.
keywords: rbac de automatização, controlo de acesso baseado em funções , rbac do azure
services: automation
ms.subservice: shared-capabilities
ms.date: 05/17/2018
ms.topic: conceptual
ms.openlocfilehash: 8caf502db91ab09eea48fc8a902dacf6bf40f24c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278639"
---
# <a name="role-based-access-control-in-azure-automation"></a>Controlo de acesso baseado em funções na Automatização do Azure

O controlo de acesso baseado em funções (RBAC) permite uma gestão de acesso para os recursos do Azure. Utilizando o [RBAC,](../role-based-access-control/overview.md)pode segregar tarefas dentro da sua equipa e conceder apenas a quantidade de acesso aos utilizadores, grupos e aplicações de que necessitam para desempenhar em seu trabalho. Pode conceder acesso baseado em papéis aos utilizadores utilizando o portal Azure, ferramentas Azure Command-Line ou APIs de Gestão Azure.

## <a name="roles-in-automation-accounts"></a>Funções nas contas de Automação

Na Automatização do Azure, é concedido acesso ao atribuir a função RBAC adequada aos utilizadores, grupos e aplicações no âmbito da conta de Automatização. Seguem-se as funções incorporadas suportadas por uma conta de Automatização:

| **Função** | **Descrição** |
|:--- |:--- |
| Proprietário |A função Proprietário permite o acesso a todos os recursos e ações dentro de uma conta Automation, incluindo o acesso a outros utilizadores, grupos e aplicações para gerir a conta Automation. |
| Contribuinte |A função de Contribuidor permite-lhe gerir tudo, exceto modificar as permissões de acesso de outro utilizador para uma conta de Automatização. |
| Leitor |A função de Leitor permite-lhe ver todos os recursos numa conta de Automatização, mas não permite efetuar quaisquer alterações. |
| Operador de Automatização |A função Do Operador de Automação permite-lhe visualizar o nome e as propriedades do livro de execução e criar e gerir empregos para todos os livros de execução numa conta de Automação. Esta função é útil se quiser proteger os recursos da sua conta Automation, como credenciais de ativos e livros de execução de serem vistos ou modificados, mas ainda assim permitir que os membros da sua organização executem estes livros de execução. |
|Operador de trabalho de automação|A função de Operador de Trabalho automational permite-lhe criar e gerir postos de trabalho para todos os livros de execução numa conta de Automação.|
|Operador de livro de automação|A função de Operador de Executo Automation Runbook permite-lhe visualizar o nome e as propriedades de um livro de execução.|
| Contribuidor do Log Analytics | A função Log Analytics Contributor permite-lhe ler todos os dados de monitorização e editar as definições de monitorização. As definições de monitorização de edição incluem a adição da extensão VM aos VMs, as chaves da conta de armazenamento de leitura para poder configurar a recolha de registos do armazenamento Azure, criar e configurar contas de Automação, adicionar soluções e configurar diagnósticos Do Azure em todos os recursos azure.|
| Leitor do Log Analytics | A função Log Analytics Reader permite-lhe visualizar e pesquisar todos os dados de monitorização, bem como visualizar as definições de monitorização. Isto inclui visualizar a configuração de diagnósticos Azure em todos os recursos do Azure. |
| Colaborador de Monitorização | A função De Colaborador de Monitorização permite-lhe ler todos os dados de monitorização e definições de monitorização da atualização.|
| Leitor de Monitorização | A função Monitoring Reader permite-lhe ler todos os dados de monitorização. |
| Administrador de Acesso de Utilizador |A função de Administrador de Acesso de Utilizador permite-lhe gerir o acesso de utilizador para as contas de Automatização do Azure. |

## <a name="role-permissions"></a>Permissões de papéis

As tabelas que se seguem descrevem as permissões específicas dadas a cada papel. Isto pode incluir Ações, que dão permissões, e NotActions, que as restringem.

### <a name="owner"></a>Proprietário

Um Proprietário pode gerir tudo, incluindo acesso. O quadro seguinte mostra as permissões concedidas para o papel:

|Ações|Descrição|
|---|---|
|Microsoft.Automation/automationAccounts/|Criar e gerir recursos de todos os tipos.|

### <a name="contributor"></a>Contribuinte

Um Colaborador pode gerir tudo menos acesso. A tabela que se segue mostra as permissões concedidas e negadas para o papel:

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Criar e gerir recursos de todos os tipos|
|**Não Ações**||
|Microsoft.Authorization/*/Delete| Eliminar papéis e atribuições de papéis.       |
|Microsoft.Authorization/*/Write     |  Criar papéis e atribuições de papéis.       |
|Microsoft.Authorization/elevateAccess/Action    | Nega a capacidade de criar um Administrador de Acesso ao Utilizador.       |

### <a name="reader"></a>Leitor

Um Leitor pode ver todos os recursos numa conta de Automação, mas não pode fazer alterações.

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Ver todos os recursos numa conta de Automação. |

### <a name="automation-operator"></a>Operador de Automatização

Um Operador de Automação é capaz de criar e gerir empregos, e ler nomes e propriedades de livros de execução para todos os livros de execução numa conta de Automação.  Nota: Se pretender controlar o acesso do operador a livros individuais, não desempenes esta função e utilize as funções de "Automation Job Operator" e "Automation Runbook Operator" em combinação. O quadro seguinte mostra as permissões concedidas para o papel:

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Authorization/*/read|Leia a autorização.|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|Leia os Recursos Operários do Livro Híbrido.|
|Microsoft.Automação/automatizaçãoContas/empregos/leitura|Lista de trabalhos do livro de corridas.|
|Microsoft.Automação/automatizaçãoContas/empregos/currículo/ação|Retome um trabalho que seja pausado.|
|Microsoft.Automação/automatizaçãoContas/empregos/stop/action|Cancele um trabalho em andamento.|
|Microsoft.Automação/automatizaçãoContas/empregos/streams/read|Leia as Correntes de Emprego e saída.|
|Microsoft.Automação/automatizaçãoContas/empregos/saída/leitura|Obter a saída de um emprego.|
|Microsoft.Automação/automatizaçãoContas/empregos/suspensão/ação|Pausa um trabalho em andamento.|
|Microsoft.Automação/automatizaçãoContas/empregos/escrita|Criar empregos.|
|Microsoft.Automação/automatizaçãoContas/jobAgendas/read|Arranja um horário de trabalho da Automação Azure.|
|Microsoft.Automação/automatizaçãoContas/jobAgendas/write|Crie um horário de trabalho da Automação Azure.|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|Obtenha o espaço de trabalho ligado à conta de Automação.|
|Microsoft.Automation/automationAccounts/read|Arranja uma conta azure Automation.|
|Microsoft.Automação/automatizaçãoContas/livros de execução/leitura|Arranja um livro de execução da Automação Azure.|
|Microsoft.Automação/automatizaçãoContas/horários/leitura|Obtenha um ativo de programação de Automação Azure.|
|Microsoft.Automação/automatizaçãoContas/horários/escrita|Criar ou atualizar um ativo de programação da Automação Azure.|
|Microsoft.Recursos/subscrições/recursosGroups/read      |Ler papéis e atribuições de papéis.         |
|Microsoft.Recursos/implementações/*      |Criar e gerir as implementações de grupos de recursos.         |
|Microsoft.Insights/alertRules/*      | Criar e gerir regras de alerta.        |
|Microsoft.Support/* |Crie e gereos bilhetes de apoio.|

### <a name="automation-job-operator"></a>Operador de trabalho de automação

Uma função de Operador de Trabalho automational é concedida no âmbito da conta Automation.Isto permite ao operador permissões para criar e gerir postos de trabalho para todos os livros de execução na conta. O quadro seguinte mostra as permissões concedidas para o papel:

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Authorization/*/read|Leia a autorização.|
|Microsoft.Automação/automatizaçãoContas/empregos/leitura|Lista de trabalhos do livro de corridas.|
|Microsoft.Automação/automatizaçãoContas/empregos/currículo/ação|Retome um trabalho que seja pausado.|
|Microsoft.Automação/automatizaçãoContas/empregos/stop/action|Cancele um trabalho em andamento.|
|Microsoft.Automação/automatizaçãoContas/empregos/streams/read|Leia as Correntes de Emprego e saída.|
|Microsoft.Automação/automatizaçãoContas/empregos/suspensão/ação|Pausa um trabalho em andamento.|
|Microsoft.Automação/automatizaçãoContas/empregos/escrita|Criar empregos.|
|Microsoft.Recursos/subscrições/recursosGroups/read      |  Ler papéis e atribuições de papéis.       |
|Microsoft.Recursos/implementações/*      |Criar e gerir as implementações de grupos de recursos.         |
|Microsoft.Insights/alertRules/*      | Criar e gerir regras de alerta.        |
|Microsoft.Support/* |Crie e gereos bilhetes de apoio.|

### <a name="automation-runbook-operator"></a>Operador de livro de automação

Uma função de Operador de Resta de Automação é concedida no âmbito do Livro de Execução. Um Operador de Livro de Automação pode ver o nome e propriedades do livro de execução.Esta função aliada à função "Automation Job Operator" permite ao operador também criar e gerir postos de trabalho para o livro de execução. O quadro seguinte mostra as permissões concedidas para o papel:

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Automação/automatizaçãoContas/livros de execução/leitura     | Lista os livros de execução.        |
|Microsoft.Authorization/*/read      | Leia a autorização.        |
|Microsoft.Recursos/subscrições/recursosGroups/read      |Ler papéis e atribuições de papéis.         |
|Microsoft.Recursos/implementações/*      | Criar e gerir as implementações de grupos de recursos.         |
|Microsoft.Insights/alertRules/*      | Criar e gerir regras de alerta.        |
|Microsoft.Support/*      | Crie e gereos bilhetes de apoio.        |

### <a name="log-analytics-contributor"></a>Contribuidor do Log Analytics

Um Colaborador de Log Analytics pode ler todos os dados de monitorização e editar definições de monitorização. As definições de monitorização de edição incluem a adição da extensão VM aos VMs; chaves da conta de armazenamento de leitura para poder configurar a recolha de registos do Armazenamento Azure; criação e configuração de contas de Automação; adicionando soluções; e configurar diagnósticos Azure em todos os recursos do Azure. O quadro seguinte mostra as permissões concedidas para o papel:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/ler|Leia os recursos de todos os tipos, exceto segredos.|
|Microsoft.Automation/automationAccounts/*|Gerir as contas da Automação.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Crie e gerencie extensões de máquinas virtuais.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Lista as chaves clássicas da conta de armazenamento.|
|Microsoft.Compute/virtualMachines/extensions/*|Crie e gerencie extensões clássicas de máquinavirtual.|
|Microsoft.Insights/alertRules/*|Leia/escreva/apague as regras de alerta.|
|Microsoft.Insights/diagnósticoDefinições/*|Ler/escrever/eliminar as definições de diagnóstico.|
|Microsoft.OperationalInsights/*|Gerencie os registos do Monitor Azure.|
|Microsoft.OperationsManagement/*|Gerir soluções em espaços de trabalho.|
|Microsoft.Recursos/implementações/*|Criar e gerir as implementações de grupos de recursos.|
|Microsoft.Recursos/subscrições/grupos de recursos/implementações/*|Criar e gerir as implementações de grupos de recursos.|
|Microsoft.Storage/storageAccounts/listKeys/action|Lista de chaves da conta de armazenamento.|
|Microsoft.Support/*|Crie e gereos bilhetes de apoio.|

### <a name="log-analytics-reader"></a>Leitor do Log Analytics

Um Leitor de Log Analytics pode visualizar e pesquisar todos os dados de monitorização, bem como visualizar as definições de monitorização, incluindo visualizar a configuração de diagnósticos Azure em todos os recursos do Azure. O quadro seguinte mostra as permissões concedidas ou negadas para o papel:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/ler|Leia os recursos de todos os tipos, exceto segredos.|
|Microsoft.OperationalInsights/workspaces/analytics/consulta/ação|Gerencie consultas em registos do Monitor Azure.|
|Microsoft.OperationalInsights/workspaces/search/action|Pesquisar dados de registo do Monitor Azure.|
|Microsoft.Support/*|Crie e gereos bilhetes de apoio.|
|**Não Ações**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Não é capaz de ler as chaves de acesso partilhadas.|

### <a name="monitoring-contributor"></a>Colaborador de Monitorização

Um Colaborador de Monitorização pode ler todos os dados de monitorização e definições de monitorização da atualização. O quadro seguinte mostra as permissões concedidas para o papel:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/ler|Leia os recursos de todos os tipos, exceto segredos.|
|Microsoft.AlertsGe/alertas/*|Gerir alertas.|
|Microsoft.AlertsManagement/alertsSSummary/*|Gerencie o painel de alerta.|
|Microsoft.Insights/AlertRules/*|Gerir as regras de alerta.|
|Microsoft.Insights/componentes/*|Gerir componentes de Insights de Aplicação.|
|Microsoft.Insights/DiagnosticSettings/*|Gerir as definições de diagnóstico.|
|Microsoft.Insights/tipos de eventos/*|Lista de eventos de Registo de Atividades (eventos de gestão) numa subscrição. Esta permissão é aplicável tanto ao acesso programático como ao portal ao Registo de Atividades.|
|Microsoft.Insights/LogDefinitions/*|Esta permissão é necessária para os utilizadores que necessitem de acesso a Registos de Atividade através do portal. Lista as categorias de registo no Registo de Atividades.|
|Microsoft.Insights/MetricDefinitions/*|Ler definições métricas (lista dos tipos métricos disponíveis para um recurso).|
|Microsoft.Insights/Metrics/*|Leia as métricas para um recurso.|
|Microsoft.Insights/Register/Action|Registe o fornecedor Microsoft.Insights.|
|Microsoft.Insights/webtests/*|Gerir os testes web da Application Insights.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Gerir pacotes de soluções de logs Azure Monitor.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Gerencie os registos do Monitor Azure, pesquisas guardadas.|
|Microsoft.OperationalInsights/workspaces/search/action|Pesquisar espaços de trabalho de Análise de Registo.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Lista as chaves para um espaço de trabalho de Log Analytics.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Gerencie as configurações de informação de armazenamento de registos do Azure Monitor.|
|Microsoft.Support/*|Crie e gereos bilhetes de apoio.|
|Microsoft.WorkloadMonitor/cargas de trabalho/*|Gerir cargas de trabalho.|

### <a name="monitoring-reader"></a>Leitor de Monitorização

Um Leitor de Monitorização pode ler todos os dados de monitorização. O quadro seguinte mostra as permissões concedidas para o papel:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/ler|Leia os recursos de todos os tipos, exceto segredos.|
|Microsoft.OperationalInsights/workspaces/search/action|Pesquisar espaços de trabalho de Análise de Registo.|
|Microsoft.Support/*|Criar e gerir bilhetes de apoio|

### <a name="user-access-administrator"></a>Administrador de Acesso de Utilizador

Um Administrador de Acesso ao Utilizador pode gerir o acesso dos utilizadores aos recursos do Azure. O quadro seguinte mostra as permissões concedidas para o papel:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/ler|Leia todos os recursos|
|Microsoft.Authorization/*|Gerir a autorização|
|Microsoft.Support/*|Criar e gerir bilhetes de apoio|

## <a name="onboarding"></a>Inclusão

As tabelas que se seguem mostram as permissões mínimas necessárias para o embarque de máquinas virtuais para as soluções de rastreio de alterações ou de atualização.

### <a name="onboarding-from-a-virtual-machine"></a>Embarque de uma máquina virtual

|**Ação**  |**Permissão**  |**Âmbito mínimo**  |
|---------|---------|---------|
|Escreva nova implantação      | Microsoft.Recursos/implementações/*          |Subscrição          |
|Escreva um novo grupo de recursos      | Microsoft.Recursos/subscrições/recursosGroups/write        | Subscrição          |
|Criar novo espaço de trabalho padrão      | Microsoft.OperationalInsights/workspaces/write         | Grupo de recursos         |
|Criar nova Conta      |  Microsoft.Automation/automationAccounts/write        |Grupo de recursos         |
|Link espaço de trabalho e conta      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|Área de trabalho</br>Conta de automatização
|Criar extensão de MMA      | Microsoft.Compute/virtualMachines/write         | Máquina Virtual         |
|Criar pesquisa guardada      | Microsoft.OperationalInsights/workspaces/write          | Área de trabalho         |
|Criar config de âmbito      | Microsoft.OperationalInsights/workspaces/write          | Área de trabalho         |
|Verificação do estado de embarque - Leia o espaço de trabalho      | Microsoft.OperationalInsights/workspaces/read         | Área de trabalho         |
|Verificação do estado de embarque - Ler propriedade de espaço de trabalho ligado da conta     | Microsoft.Automation/automationAccounts/read      | Conta de automatização        |
|Verificação do estado de embarque - Leia a solução      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Solução         |
|Verificação do estado de embarque - Ler VM      | Microsoft.Compute/virtualMachines/read         | Máquina Virtual         |
|Verificação do estado de embarque - Leia a conta      | Microsoft.Automation/automationAccounts/read  |  Conta de automatização   |
| Verificação do espaço de trabalho de embarque para VM<sup>1</sup>       | Microsoft.OperationalInsights/workspaces/read         | Subscrição         |
| Registe o fornecedor Log Analytics |Microsoft.Insights/register/action | Subscrição|

<sup>1</sup> Esta permissão é necessária para embarcar através da experiência do portal VM.

### <a name="onboarding-from-automation-account"></a>Embarque na conta automation

|**Ação**  |**Permissão** |**Âmbito mínimo**  |
|---------|---------|---------|
|Criar uma nova implementação     | Microsoft.Recursos/implementações/*        | Subscrição         |
|Criar um novo grupo de recursos     | Microsoft.Recursos/subscrições/recursosGroups/write         | Subscrição        |
|AutomationOnboarding blade - Criar novo espaço de trabalho     |Microsoft.OperationalInsights/workspaces/write           | Grupo de recursos        |
|AutomationOnboarding blade - ler espaço de trabalho ligado     | Microsoft.Automation/automationAccounts/read        | Conta de automatização       |
|AutomationOnboarding blade - solução de leitura     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Solução        |
|AutomationOnboarding blade - leia espaço de trabalho     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Área de trabalho        |
|Criar link para espaço de trabalho e conta     | Microsoft.OperationalInsights/workspaces/write        | Área de trabalho        |
|Escrever conta para caixa de sapatos      | Microsoft.Automation/automationAccounts/write        | Conta        |
|Criar/editar pesquisa guardada     | Microsoft.OperationalInsights/workspaces/write        | Área de trabalho        |
|Criar/editar config de âmbito     | Microsoft.OperationalInsights/workspaces/write        | Área de trabalho        |
| Registe o fornecedor Log Analytics |Microsoft.Insights/register/action | Subscrição|
|**Passo 2 - VMs a bordo**     |         |         |
|Lâmina VMOnboarding - Criar extensão MMA     | Microsoft.Compute/virtualMachines/write           | Máquina Virtual        |
|Criar /editar pesquisa guardada     | Microsoft.OperationalInsights/workspaces/write           | Área de trabalho        |
|Criar/editar o config de âmbito de aplicação  | Microsoft.OperationalInsights/workspaces/write   | Área de trabalho|

## <a name="update-management"></a>Gestão de atualizações

A gestão de atualizações chega a vários serviços para prestar o seu serviço. O quadro seguinte mostra as permissões necessárias para gerir as implementações de gestão de atualizações:

|**Recurso**  |**Função**  |**Âmbito**  |
|---------|---------|---------|
|Conta de automatização     | Contribuidor do Log Analytics       | Conta de automatização        |
|Conta de automatização    | Contribuidor de Máquina Virtual        | Grupo de Recursos para a conta        |
|Área de trabalho do Log Analytics     | Contribuidor do Log Analytics| Área de trabalho do Log Analytics        |
|Área de trabalho do Log Analytics |Leitor do Log Analytics| Subscrição|
|Solução     |Contribuidor do Log Analytics         | Solução|
|Máquina Virtual     | Contribuidor de Máquina Virtual        | Máquina Virtual        |

## <a name="configure-rbac-for-your-automation-account"></a>Configure rBAC para a sua conta de Automação

A secção seguinte mostra como configurar o RBAC na sua conta Automation através do [portal](#configure-rbac-using-the-azure-portal) e [powerShell](#configure-rbac-using-powershell).

### <a name="configure-rbac-using-the-azure-portal"></a>Configure rBAC utilizando o portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) e abra a sua conta de Automatização a partir da página Contas de Automatização.
2. Clique no controlo de **acesso (IAM)** no canto superior esquerdo para abrir a página de controlo de acesso (IAM). Pode utilizar esta página para adicionar novos utilizadores, grupos e aplicações para gerir a sua conta De automação e visualizar as funções existentes que são configuráveis para a conta Automation.
3. Clique no separador **Atribuições de funções**.

   ![Botão de acesso](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Adicionar um novo utilizador e atribuir uma função

1. A partir da página de controlo de acesso (IAM), clique **+ Adicionar atribuição de funções**. Esta ação abre a página de atribuição de funções Adicionar onde pode adicionar um utilizador, grupo ou aplicação, e atribuir uma função correspondente.

2. Selecione uma função na lista de funções disponíveis. Pode escolher qualquer uma das funções incorporadas disponíveis que uma conta Automation suporte ou qualquer papel personalizado que possa ter definido.

3. Digite o nome de utilizador do utilizador a que pretende dar permissões no campo **Select.** Escolha o utilizador da lista e clique em **Guardar**.

   ![Adicionar utilizadores](media/automation-role-based-access-control/automation-04-add-users.png)

   Agora deverá consultar o utilizador adicionado à página Utilizadores, com a função selecionada atribuída.

   ![Listar utilizadores](media/automation-role-based-access-control/automation-05-list-users.png)

   Também pode atribuir uma função ao utilizador a partir da página Funções.
4. Clique em **Funções** a partir da página de controlo de acesso (IAM) para abrir a página Funções. Pode ver o nome do papel e o número de utilizadores e grupos atribuídos a esse papel.

    ![Atribuir função a partir da página Utilizadores](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Só é possível definir o controlo de acesso baseado em funções no âmbito da conta Automation e não em nenhum recurso abaixo da conta Automation.

#### <a name="remove-a-user"></a>Remover um utilizador

Pode remover a permissão de acesso para um utilizador que não esteja a gerir a conta Automation, ou que já não trabalhe para a organização. Seguem-se os passos para remover um utilizador:

1. A partir da página de controlo de acesso (IAM), selecione o utilizador para remover e clicar **Em Remover**.
2. Clique no botão **Remover** na página de detalhes de atribuição.
3. Clique em **Sim** para confirmar a remoção.

   ![Remover utilizadores](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-rbac-using-powershell"></a>Configure RBAC usando powerShell

Também pode configurar o acesso baseado em funções a uma conta Automation utilizando os [seguintes cmdlets Azure PowerShell:](../role-based-access-control/role-assignments-powershell.md)

[Get-AzureRmRoleDefinition](/previous-versions/azure/mt603792(v=azure.100)) lista todas as funções RBAC que estão disponíveis no Diretório Ativo Azure. Pode utilizar este cmdlet com o parâmetro *Nome* para enumerar todas as ações que uma função específica pode desempenhar.

```azurepowershell-interactive
Get-AzureRmRoleDefinition -Name 'Automation Operator'
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

[Get-AzureRmRoleAssignment](/previous-versions/azure/mt619413(v=azure.100)) lista atribuições de funções Azure AD RBAC no âmbito especificado. Sem parâmetros, este cmdlet devolve todas as atribuições de funções efetuadas ao abrigo da subscrição. Utilize o parâmetro *ExpandPrincipalGroups* para listar as atribuições de acesso ao utilizador especificado, bem como os grupos a que o utilizador pertence.

**Exemplo:** Utilize o seguinte cmdlet para listar todos os utilizadores e suas funções dentro de uma conta Automation.

```azurepowershell-interactive
Get-AzureRMRoleAssignment -scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
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

Utilize a [New-AzureRmRoleAssignment](/previous-versions/azure/mt603580(v=azure.100)) para atribuir acesso a utilizadores, grupos e aplicações a um determinado âmbito.
    
**Exemplo:** Utilize o seguinte comando para atribuir a função "Operador de Automação" a um utilizador no âmbito da conta Automation.

```azurepowershell-interactive
New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
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

Utilize [a Eliminação-AzureRmRoleAssignment](/previous-versions/azure/mt603781(v=azure.100)) para remover o acesso de um utilizador, grupo ou aplicação especificado a partir de um determinado âmbito.

**Exemplo:** Utilize o seguinte comando para remover o utilizador da função "Operador de Automação" no âmbito da conta Automation.

```azurepowershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Nos exemplos anteriores, substitua "iD de inscrição de um utilizador que deseje remover", "Subscrição ID", "Nome do Grupo de Recursos" e nome da conta Automation" pelos detalhes da sua conta. Escolha **sim** quando solicitado a confirmar antes de continuar a remover as atribuições de funções do utilizador.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Experiência do utilizador para a função do operador de automação - Conta de automação

Quando um utilizador designado para a função de Operador de Automação na conta Automation scope vê a conta Automation a que é atribuído, o utilizador só pode ver a lista de livros de execução, trabalhos de livro e horários criados na conta Automation. Este utilizador não pode ver as definições destes itens. O utilizador pode iniciar, parar, suspender, retomar ou agendar o trabalho do livro de recortes. No entanto, o utilizador não tem acesso a outros recursos da Automação, tais como configurações, grupos de trabalhadores híbridos ou nós DSC.

![Sem acesso a recursos](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-rbac-for-runbooks"></a>Configure RBAC para livros de execução

A Azure Automation permite-lhe atribuir RBAC a livros de execução específicos. Para tal, execute o seguinte script para adicionar um utilizador a um livro de execução específico. Um Administrador de Conta de Automação ou um Administrador inquilino podem executar este script.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation account
$automationAccountName ="<Automation account name>" # Name of the Automation account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation account resource
$aa = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$automationAccountName/$rbName"

# The Automation Job Operator role only needs to be run once per user.
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

Uma vez que o script tenha sido executado, faça o utilizador iniciar sessão no portal Azure e ver **Todos os Recursos**. Na lista, o utilizador pode ver o livro de execução para o qual foi adicionado como Um Operador de Resta de Automação.

![RBAC do livro de corridas no portal](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Experiência do utilizador para função de operador de automação - Runbook

Quando um utilizador designado para a função de Operador de Automação no âmbito do Livro de Execução visualiza um livro de execução atribuído, o utilizador só pode iniciar o livro de execução e ver os trabalhos do livro de execução.

![Só tem acesso para começar](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre formas de configurar o RBAC para a Automação Azure, consulte a gestão do [RBAC com a Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Para mais detalhes sobre formas de iniciar um livro de corridas, consulte Iniciar um livro de [corridas](automation-starting-a-runbook.md).
* Para obter informações sobre os tipos de livro de execução, consulte os tipos de livro de [execução da Automatização Azure](automation-runbook-types.md).