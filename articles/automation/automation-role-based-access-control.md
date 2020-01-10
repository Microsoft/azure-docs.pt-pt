---
title: Controlo de acesso baseado em funções na Automatização do Azure
description: O controlo de acesso baseado em funções (RBAC) permite uma gestão de acesso para os recursos do Azure. Este artigo descreve como configurar o RBAC na Automatização do Azure.
keywords: rbac de automatização, controlo de acesso baseado em funções , rbac do azure
services: automation
ms.subservice: shared-capabilities
ms.date: 05/17/2018
ms.topic: conceptual
ms.openlocfilehash: 122e1f69e952acc00aba3cad2d75cb87b8fd08ee
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421581"
---
# <a name="role-based-access-control-in-azure-automation"></a>Controlo de acesso baseado em funções na Automatização do Azure

O controlo de acesso baseado em funções (RBAC) permite uma gestão de acesso para os recursos do Azure. Usando o [RBAC](../role-based-access-control/overview.md), você pode separar as tarefas dentro de sua equipe e conceder apenas a quantidade de acesso a usuários, grupos e aplicativos de que eles precisam para executar seus trabalhos. O acesso baseado em funções pode ser concedido aos utilizadores através do portal do Azure, das ferramentas da Linha de Comandos do Azure ou de APIs de Gestão do Azure.

## <a name="roles-in-automation-accounts"></a>Funções em contas de automação

Na Automatização do Azure, é concedido acesso ao atribuir a função RBAC adequada aos utilizadores, grupos e aplicações no âmbito da conta de Automatização. Seguem-se as funções incorporadas suportadas por uma conta de Automatização:

| **Função** | **Descrição** |
|:--- |:--- |
| Proprietário |A função proprietário permite acesso a todos os recursos e ações em uma conta de automação, incluindo o fornecimento de acesso a outros usuários, grupos e aplicativos para gerenciar a conta de automação. |
| Contribuinte |A função de Contribuidor permite-lhe gerir tudo, exceto modificar as permissões de acesso de outro utilizador para uma conta de Automatização. |
| Leitor |A função de Leitor permite-lhe ver todos os recursos numa conta de Automatização, mas não permite efetuar quaisquer alterações. |
| Operador de Automatização |A função operador de automação permite exibir o nome e as propriedades do runbook e criar e gerenciar trabalhos para todos os runbooks em uma conta de automação. Esta função é útil se pretender proteger os seus recursos da Conta de Automatização como recursos de credenciais e runbooks contra a visualização ou modificação, mas ainda permite que os membros da sua organização executem estes runbooks. |
|Operador de trabalho de automação|A função operador de trabalho de automação permite criar e gerenciar trabalhos para todos os runbooks em uma conta de automação.|
|Operador de runbook de automação|A função de operador de runbook de automação permite que você exiba o nome e as propriedades de um runbook.|
| Contribuidor do Log Analytics | A função colaborador de Log Analytics permite que você leia todos os dados de monitoramento e edite as configurações de monitoramento. A edição das configurações de monitoramento inclui adicionar a extensão de VM às VMs, lendo as chaves da conta de armazenamento para poder configurar a coleta de logs do armazenamento do Azure, criar e configurar contas de automação, adicionar soluções e configurar o diagnóstico do Azure em todos os recursos do Azure.|
| Leitor do Log Analytics | A função leitor de Log Analytics permite exibir e Pesquisar todos os dados de monitoramento, bem como exibir configurações de monitoramento. Isso inclui a exibição da configuração do diagnóstico do Azure em todos os recursos do Azure. |
| Colaborador de monitoramento | A função de colaborador de monitoramento permite que você leia todos os dados de monitoramento e atualize as configurações de monitoramento.|
| Leitor de monitoramento | A função leitor de monitoramento permite que você leia todos os dados de monitoramento. |
| Administrador de Acesso de Utilizador |A função de Administrador de Acesso de Utilizador permite-lhe gerir o acesso de utilizador para as contas de Automatização do Azure. |

## <a name="role-permissions"></a>Permissões de função

As tabelas a seguir descrevem as permissões específicas dadas a cada função. Isso pode incluir ações, que fornecem permissões e não ações, que as restringem.

### <a name="owner"></a>Proprietário

Um proprietário pode gerenciar tudo, incluindo o acesso. A tabela a seguir mostra as permissões concedidas para a função:

|Ações|Descrição|
|---|---|
|Microsoft.Automation/automationAccounts/|Crie e gerencie recursos de todos os tipos.|

### <a name="contributor"></a>Contribuinte

Um colaborador pode gerenciar tudo, exceto o acesso. A tabela a seguir mostra as permissões concedidas e negadas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Criar e gerenciar recursos de todos os tipos|
|**Não há ações**||
|Microsoft.Authorization/*/Delete| Excluir funções e atribuições de função.       |
|Microsoft. Authorization/*/Write     |  Criar funções e atribuições de função.       |
|Microsoft.Authorization/elevateAccess/Action    | Nega a capacidade de criar um administrador de acesso do usuário.       |

### <a name="reader"></a>Leitor

Um leitor pode exibir todos os recursos em uma conta de automação, mas não pode fazer nenhuma alteração.

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Exibir todos os recursos em uma conta de automação. |

### <a name="automation-operator"></a>Operador de Automatização

Um operador de automação é capaz de criar e gerenciar trabalhos e ler nomes e propriedades de runbook para todos os runbooks em uma conta de automação.  Observação: se você quiser controlar o acesso do operador a runbooks individuais, não defina essa função e, em vez disso, use as funções ' operador de trabalho de automação ' e ' operador de runbook de automação ' em combinação. A tabela a seguir mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Authorization/*/read|Leitura de autorização.|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|Leia Hybrid Runbook Worker recursos.|
|Microsoft.Automation/automationAccounts/jobs/read|Listar trabalhos do runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Retomar um trabalho que está em pausa.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Cancelar um trabalho em andamento.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Leia os fluxos de trabalho e a saída.|
|Microsoft.Automation/automationAccounts/jobs/output/read|Obter a saída de um trabalho.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Pausar um trabalho em andamento.|
|Microsoft.Automation/automationAccounts/jobs/write|Criar trabalhos.|
|Microsoft.Automation/automationAccounts/jobSchedules/read|Obter um plano de trabalho de automação do Azure.|
|Microsoft.Automation/automationAccounts/jobSchedules/write|Criar um plano de trabalho de automação do Azure.|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|Obtenha o espaço de trabalho vinculado à conta de automação.|
|Microsoft.Automation/automationAccounts/read|Obtenha uma conta de automação do Azure.|
|Microsoft.Automation/automationAccounts/runbooks/read|Obtenha um runbook de automação do Azure.|
|Microsoft.Automation/automationAccounts/schedules/read|Obtenha um ativo de agendamento de automação do Azure.|
|Microsoft.Automation/automationAccounts/schedules/write|Criar ou atualizar um ativo de agendamento de automação do Azure.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Ler funções e atribuições de função.         |
|Microsoft.Resources/deployments/*      |Crie e gerencie implantações de grupo de recursos.         |
|Microsoft.Insights/alertRules/*      | Crie e gerencie regras de alerta.        |
|Microsoft.Support/* |Criar e gerenciar tíquetes de suporte.|

### <a name="automation-job-operator"></a>Operador de trabalho de automação

Uma função de operador de trabalho de automação é concedida no escopo da conta de automação. Isso permite que as permissões de operador criem e gerenciem trabalhos para todos os runbooks na conta. A tabela a seguir mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Authorization/*/read|Leitura de autorização.|
|Microsoft.Automation/automationAccounts/jobs/read|Listar trabalhos do runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Retomar um trabalho que está em pausa.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Cancelar um trabalho em andamento.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Leia os fluxos de trabalho e a saída.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Pausar um trabalho em andamento.|
|Microsoft.Automation/automationAccounts/jobs/write|Criar trabalhos.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Ler funções e atribuições de função.       |
|Microsoft.Resources/deployments/*      |Crie e gerencie implantações de grupo de recursos.         |
|Microsoft.Insights/alertRules/*      | Crie e gerencie regras de alerta.        |
|Microsoft.Support/* |Criar e gerenciar tíquetes de suporte.|

### <a name="automation-runbook-operator"></a>Operador de runbook de automação

Uma função de operador de runbook de automação é concedida no escopo do runbook. Um operador de runbook de automação pode exibir o nome e as propriedades do runbook.  Essa função combinada com a função ' operador de trabalho de automação ' permite que o operador também crie e gerencie trabalhos para o runbook. A tabela a seguir mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Liste os runbooks.        |
|Microsoft.Authorization/*/read      | Leitura de autorização.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Ler funções e atribuições de função.         |
|Microsoft.Resources/deployments/*      | Crie e gerencie implantações de grupo de recursos.         |
|Microsoft.Insights/alertRules/*      | Crie e gerencie regras de alerta.        |
|Microsoft.Support/*      | Criar e gerenciar tíquetes de suporte.        |

### <a name="log-analytics-contributor"></a>Contribuidor do Log Analytics

Um colaborador de Log Analytics pode ler todos os dados de monitoramento e editar as configurações de monitoramento. Editar configurações de monitoramento inclui adicionar a extensão de VM às VMs; leitura de chaves de conta de armazenamento para poder configurar a coleta de logs do armazenamento do Azure; Criando e Configurando contas de automação; adicionando soluções; e configurar o diagnóstico do Azure em todos os recursos do Azure. A tabela a seguir mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/read|Ler recursos de todos os tipos, exceto segredos.|
|Microsoft.Automation/automationAccounts/*|Gerenciar contas de automação.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Criar e gerenciar extensões de máquina virtual.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Listar chaves de conta de armazenamento clássico.|
|Microsoft.Compute/virtualMachines/extensions/*|Criar e gerenciar extensões de máquina virtual clássicas.|
|Microsoft.Insights/alertRules/*|Regras de alerta de leitura/gravação/exclusão.|
|Microsoft.Insights/diagnosticSettings/*|Configurações de diagnóstico de leitura/gravação/exclusão.|
|Microsoft.OperationalInsights/*|Gerenciar logs de Azure Monitor.|
|Microsoft.OperationsManagement/*|Gerencie soluções em espaços de trabalho.|
|Microsoft.Resources/deployments/*|Crie e gerencie implantações de grupo de recursos.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Crie e gerencie implantações de grupo de recursos.|
|Microsoft.Storage/storageAccounts/listKeys/action|Listar chaves de conta de armazenamento.|
|Microsoft.Support/*|Criar e gerenciar tíquetes de suporte.|

### <a name="log-analytics-reader"></a>Leitor do Log Analytics

Um leitor de Log Analytics pode exibir e Pesquisar todos os dados de monitoramento, bem como exibir configurações de monitoramento, incluindo a exibição da configuração do diagnóstico do Azure em todos os recursos do Azure. A tabela a seguir mostra as permissões concedidas ou negadas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/read|Ler recursos de todos os tipos, exceto segredos.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Gerenciar consultas em logs de Azure Monitor.|
|Microsoft.OperationalInsights/workspaces/search/action|Pesquisar Azure Monitor dados de log.|
|Microsoft.Support/*|Criar e gerenciar tíquetes de suporte.|
|**Não há ações**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Não é possível ler as chaves de acesso compartilhado.|

### <a name="monitoring-contributor"></a>Colaborador de monitoramento

Um colaborador de monitoramento pode ler todos os dados de monitoramento e atualizar as configurações de monitoramento. A tabela a seguir mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/read|Ler recursos de todos os tipos, exceto segredos.|
|Microsoft.AlertsManagement/alerts/*|Gerenciar alertas.|
|Microsoft.AlertsManagement/alertsSummary/*|Gerencie o painel de alerta.|
|Microsoft.Insights/AlertRules/*|Gerenciar regras de alerta.|
|Microsoft.Insights/components/*|Gerenciar componentes de Application Insights.|
|Microsoft.Insights/DiagnosticSettings/*|Gerenciar configurações de diagnóstico.|
|Microsoft.Insights/eventtypes/*|Listar eventos do log de atividades (eventos de gerenciamento) em uma assinatura. Essa permissão é aplicável a acesso de portal e programático ao log de atividades.|
|Microsoft.Insights/LogDefinitions/*|Essa permissão é necessária para os usuários que precisam acessar os logs de atividade por meio do Portal. Listar categorias de log no log de atividades.|
|Microsoft.Insights/MetricDefinitions/*|Ler definições de métricas (lista de tipos de métrica disponíveis para um recurso).|
|Microsoft.Insights/Metrics/*|Ler métricas para um recurso.|
|Microsoft.Insights/Register/Action|Registre o provedor Microsoft. insights.|
|Microsoft.Insights/webtests/*|Gerenciar Application Insights testes da Web.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Gerenciar Azure Monitor os pacotes de solução de logs.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Gerenciar Azure Monitor logs de pesquisas salvas.|
|Microsoft.OperationalInsights/workspaces/search/action|Pesquisar Log Analytics espaços de trabalho.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Listar chaves para um espaço de trabalho Log Analytics.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Gerenciar Azure Monitor registra as configurações de insights de armazenamento.|
|Microsoft.Support/*|Criar e gerenciar tíquetes de suporte.|
|Microsoft.WorkloadMonitor/workloads/*|Gerenciar cargas de trabalho.|

### <a name="monitoring-reader"></a>Leitor de monitoramento

Um leitor de monitoramento pode ler todos os dados de monitoramento. A tabela a seguir mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/read|Ler recursos de todos os tipos, exceto segredos.|
|Microsoft.OperationalInsights/workspaces/search/action|Pesquisar Log Analytics espaços de trabalho.|
|Microsoft.Support/*|Criar e gerenciar tíquetes de suporte|

### <a name="user-access-administrator"></a>Administrador de Acesso de Utilizador

Um administrador de acesso do usuário pode gerenciar o acesso do usuário aos recursos do Azure. A tabela a seguir mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|*/read|Ler todos os recursos|
|Microsoft. Authorization/*|Gerenciar autorização|
|Microsoft.Support/*|Criar e gerenciar tíquetes de suporte|

## <a name="onboarding"></a>Carregamento

As tabelas a seguir mostram as permissões mínimas necessárias para a integração de máquinas virtuais para o controle de alterações ou soluções de gerenciamento de atualizações.

### <a name="onboarding-from-a-virtual-machine"></a>Integração de uma máquina virtual

|**Ação**  |**Permissão**  |**Escopo mínimo**  |
|---------|---------|---------|
|Gravar nova implantação      | Microsoft.Resources/deployments/*          |Subscrição          |
|Gravar novo grupo de recursos      | Microsoft.Resources/subscriptions/resourceGroups/write        | Subscrição          |
|Criar novo espaço de trabalho padrão      | Microsoft.OperationalInsights/workspaces/write         | Grupo de recursos         |
|Criar nova conta      |  Microsoft.Automation/automationAccounts/write        |Grupo de recursos         |
|Vincular espaço de trabalho e conta      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|Área de trabalho</br>Conta de automatização
|Criar solução      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write |Grupo de recursos          |
|Criar extensão MMA      | Microsoft.Compute/virtualMachines/write         | Virtual Machine         |
|Criar pesquisa salva      | Microsoft.OperationalInsights/workspaces/write          | Área de trabalho         |
|Criar configuração de escopo      | Microsoft.OperationalInsights/workspaces/write          | Área de trabalho         |
|Vincular solução à configuração de escopo      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Solução         |
|Verificação de estado de integração-ler espaço de trabalho      | Microsoft.OperationalInsights/workspaces/read         | Área de trabalho         |
|Verificação de estado de integração-ler propriedade de conta do espaço de trabalho vinculado     | Microsoft.Automation/automationAccounts/read      | Conta de automatização        |
|Verificação de estado de integração – ler solução      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Solução         |
|Verificação de estado de integração-ler VM      | Microsoft.Compute/virtualMachines/read         | Virtual Machine         |
|Verificação de estado de integração-ler conta      | Microsoft.Automation/automationAccounts/read  |  Conta de automatização   |
| Verificação de espaço de trabalho de integração para a VM<sup>1</sup>       | Microsoft.OperationalInsights/workspaces/read         | Subscrição         |
| Registrar o provedor de Log Analytics |Microsoft.Insights/register/action | Subscrição|

<sup>1</sup> essa permissão é necessária para integração por meio da experiência do portal de VM.

### <a name="onboarding-from-automation-account"></a>Integração da conta de automação

|**Ação**  |**Permissão** |**Escopo mínimo**  |
|---------|---------|---------|
|Criar nova implantação     | Microsoft.Resources/deployments/*        | Subscrição         |
|Criar novo grupo de recursos     | Microsoft.Resources/subscriptions/resourceGroups/write         | Subscrição        |
|Folha AutomationOnboarding – criar novo espaço de trabalho     |Microsoft.OperationalInsights/workspaces/write           | Grupo de recursos        |
|Folha AutomationOnboarding-ler espaço de trabalho vinculado     | Microsoft.Automation/automationAccounts/read        | Conta de automatização       |
|Folha AutomationOnboarding-ler solução     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Solução        |
|Folha AutomationOnboarding-ler espaço de trabalho     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Área de trabalho        |
|Criar link para o espaço de trabalho e conta     | Microsoft.OperationalInsights/workspaces/write        | Área de trabalho        |
|Gravar conta para sapatos      | Microsoft.Automation/automationAccounts/write        | Conta        |
|Criar solução      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write        | Grupo de Recursos         |
|Criar/editar pesquisa salva     | Microsoft.OperationalInsights/workspaces/write        | Área de trabalho        |
|Criar/Editar configuração de escopo     | Microsoft.OperationalInsights/workspaces/write        | Área de trabalho        |
|Vincular solução à configuração de escopo      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Solução         |
| Registrar o provedor de Log Analytics |Microsoft.Insights/register/action | Subscrição|
|**Etapa 2 – carregar várias VMs**     |         |         |
|Folha VMOnboarding – criar extensão MMA     | Microsoft.Compute/virtualMachines/write           | Virtual Machine        |
|Criar/editar pesquisa salva     | Microsoft.OperationalInsights/workspaces/write           | Área de trabalho        |
|Criar/Editar configuração de escopo  | Microsoft.OperationalInsights/workspaces/write   | Área de trabalho|

## <a name="update-management"></a>Gestão de atualizações

O gerenciamento de atualizações atinge vários serviços para fornecer seu serviço. A tabela a seguir mostra as permissões necessárias para gerenciar implantações de gerenciamento de atualizações:

|**Recurso**  |**Função**  |**Âmbito**  |
|---------|---------|---------|
|Conta de automatização     | Contribuidor do Log Analytics       | Conta de automatização        |
|Conta de automatização    | Contribuidor de Máquina Virtual        | Grupo de recursos para a conta        |
|Área de trabalho do Log Analytics     | Contribuidor do Log Analytics| Área de trabalho do Log Analytics        |
|Área de trabalho do Log Analytics |Leitor do Log Analytics| Subscrição|
|Solução     |Contribuidor do Log Analytics         | Solução|
|Virtual Machine     | Contribuidor de Máquina Virtual        | Virtual Machine        |

## <a name="configure-rbac-for-your-automation-account"></a>Configurar o RBAC para sua conta de automação

A seção a seguir mostra como configurar o RBAC em sua conta de automação por meio do [portal](#configure-rbac-using-the-azure-portal) e do [PowerShell](#configure-rbac-using-powershell)

### <a name="configure-rbac-using-the-azure-portal"></a>Configurar o RBAC usando o portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) e abra a sua conta de Automatização a partir da página Contas de Automatização.
2. Clique no controle de **controle de acesso (iam)** no canto superior esquerdo. Isso abre a página **controle de acesso (iam)** , na qual você pode adicionar novos usuários, grupos e aplicativos para gerenciar sua conta de automação e exibir as funções existentes que podem ser configuradas para a conta de automação.
3. Clique nas **atribuições de funções** separador.

   ![Botão de acesso](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Adicionar um novo utilizador e atribuir uma função

1. Na página **controle de acesso (iam)** , clique em **+ Adicionar atribuição de função** para abrir a página **Adicionar atribuição de função** , na qual é possível adicionar um usuário, grupo ou aplicativo e atribuir uma função a eles.

2. Selecione uma função na lista de funções disponíveis. Você pode escolher qualquer uma das funções internas disponíveis às quais uma conta de automação oferece suporte ou qualquer função personalizada que você tenha definido.

3. Digite o nome do usuário ao qual você deseja conceder permissões no campo **selecionar** . Selecione o usuário na lista e clique em **salvar**.

   ![Adicionar utilizadores](media/automation-role-based-access-control/automation-04-add-users.png)

   Agora você deve ver o usuário adicionado à página **usuários** com a função selecionada atribuída

   ![Listar utilizadores](media/automation-role-based-access-control/automation-05-list-users.png)

   Também pode atribuir uma função ao utilizador a partir da página **Funções**.
4. Clique em **funções** na página **controle de acesso (iam)** para abrir a página **funções** . A partir daí, pode ver o nome da função, o número de utilizadores e os grupos atribuídos a essa função.

    ![Atribuir função a partir da página Utilizadores](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > O controle de acesso baseado em função só pode ser definido no escopo da conta de automação e não em nenhum recurso abaixo da conta de automação.

#### <a name="remove-a-user"></a>Remover um utilizador

Você pode remover a permissão de acesso para um usuário que não está gerenciando a conta de automação ou que não funciona mais para a organização. Seguem-se os passos para remover um utilizador:

1. Na página **controle de acesso (iam)** , selecione o usuário deseja remover e clique em **remover**.
2. Clique no botão **Remover** na página de detalhes de atribuição.
3. Clique em **Sim** para confirmar a remoção.

   ![Remover utilizadores](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-rbac-using-powershell"></a>Configurar o RBAC usando o PowerShell

O acesso baseado em função também pode ser configurado para uma conta de automação usando os seguintes [cmdlets Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

[Get-AzureRmRoleDefinition](/previous-versions/azure/mt603792(v=azure.100)) lista todas as funções RBAC que estão disponíveis no Azure Active Directory. Pode utilizar este comando juntamente com a propriedade **Nome** para listar todas as ações que podem ser utilizadas com uma função específica.

```azurepowershell-interactive
Get-AzureRmRoleDefinition -Name 'Automation Operator'
```

Veja a seguir o exemplo de saída:

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

[Get-AzureRmRoleAssignment](/previous-versions/azure/mt619413(v=azure.100)) lista as atribuições de função do RBAC do Azure AD no escopo especificado. Sem quaisquer parâmetros, este comando devolve todas as atribuições de funções efetuadas sob a subscrição. Utilize o parâmetro **ExpandPrincipalGroups** para listar atribuições de acesso para o utilizador especificado, bem como para os grupos dos quais o utilizador é membro.
    **Exemplo:** utilize o seguinte comando para listar todos os utilizadores e as respetivas funções dentro de uma conta de automatização.

```azurepowershell-interactive
Get-AzureRMRoleAssignment -scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Veja a seguir o exemplo de saída:

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

[New-AzureRmRoleAssignment](/previous-versions/azure/mt603580(v=azure.100)) para atribuir acesso a usuários, grupos e aplicativos a um escopo específico.
    **Exemplo:** Use o comando a seguir para atribuir a função "operador de automação" para um usuário no escopo da conta de automação.

```azurepowershell-interactive
New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Veja a seguir o exemplo de saída:

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

Use [Remove-AzureRmRoleAssignment](/previous-versions/azure/mt603781(v=azure.100)) para remover o acesso de um usuário, grupo ou aplicativo especificado de um escopo específico.
    **Exemplo:** Use o comando a seguir para remover o usuário da função "operador de automação" no escopo da conta de automação.

```azurepowershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Nos exemplos anteriores, substitua **ID de entrada**, **ID da assinatura**, **nome do grupo de recursos**e nome da conta de **automação** pelos detalhes da sua conta. Escolha **Sim** quando lhe for solicitado para confirmar antes de continuar para remover a atribuição de função de utilizador.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Experiência do usuário para função do operador de automação – conta de automação

Quando um usuário, que é atribuído à função de operador de automação no escopo da conta de automação exibe a conta de automação à qual eles estão atribuídos, eles só podem exibir a lista de runbooks, trabalhos de runbook e agendamentos criados na conta de automação, mas não podem exibir seus defini. Pode iniciar, parar, suspender, retomar ou agendar a tarefa de runbook. O usuário não tem acesso a outros recursos de automação, como configurações, grupos de trabalho híbrido ou nós DSC.

![Sem acesso aos recursos](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-rbac-for-runbooks"></a>Configurar o RBAC para Runbooks

A automação do Azure permite que você atribua o RBAC a runbooks específicos. Para fazer isso, execute o script a seguir para adicionar um usuário a um runbook específico. O script a seguir pode ser executado por um administrador de conta de automação ou administrador de locatário.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation Account
$automationAccountName ="<Automation Account Name>" # Name of the Automation Account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation Account resource
$aa = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$automationAccountName/$rbName"

# The Automation Job Operator role only needs to be ran once per user.
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

Depois de executado, peça ao usuário que faça logon no portal do Azure e exiba **todos os recursos**. Na lista, eles veem o runbook que foram adicionados como um **operador de runbook de automação** para.

![Runbook RBAC no portal](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Experiência do usuário para função de operador de automação-runbook

Quando um usuário, que é atribuído à função de operador de automação no escopo do runbook exibe um runbook ao qual eles são atribuídos, eles só podem iniciar o runbook e exibir os trabalhos de runbook.

![Só tem acesso para iniciar](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre diferentes maneiras de configurar o RBAC para a Automatização do Azure, veja [Gerir o RBAC com o Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Para obter detalhes sobre diferentes formas de iniciar um runbook, consulte o artigo [Iniciar um runbook](automation-starting-a-runbook.md)
* Para obter informações sobre os vários tipos de runbook, veja [Tipos de runbook de Automatização do Azure](automation-runbook-types.md)

