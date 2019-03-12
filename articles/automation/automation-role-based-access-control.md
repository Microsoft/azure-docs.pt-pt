---
title: Controlo de acesso baseado em funções na Automatização do Azure
description: O controlo de acesso baseado em funções (RBAC) permite uma gestão de acesso para os recursos do Azure. Este artigo descreve como configurar o RBAC na Automatização do Azure.
keywords: rbac de automatização, controlo de acesso baseado em funções , rbac do azure
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b307a497e69bd6c2dcc7b415b2d94335459f7fd3
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544993"
---
# <a name="role-based-access-control-in-azure-automation"></a>Controlo de acesso baseado em funções na Automatização do Azure

O controlo de acesso baseado em funções (RBAC) permite uma gestão de acesso para os recursos do Azure. Usando [RBAC](../role-based-access-control/overview.md), pode separar responsabilidades dentro da sua equipa e conceder apenas a quantidade de acesso a utilizadores, grupos e aplicações que precisam para desempenhar as suas funções. O acesso baseado em funções pode ser concedido aos utilizadores através do portal do Azure, das ferramentas da Linha de Comandos do Azure ou de APIs de Gestão do Azure.

## <a name="roles-in-automation-accounts"></a>Funções de contas de automatização

Na Automatização do Azure, é concedido acesso ao atribuir a função RBAC adequada aos utilizadores, grupos e aplicações no âmbito da conta de Automatização. Seguem-se as funções incorporadas suportadas por uma conta de Automatização:

| **Função** | **Descrição** |
|:--- |:--- |
| Proprietário |A função de proprietário permite o acesso a todos os recursos e ações dentro de uma conta de automatização, incluindo fornecer acesso a outros utilizadores, grupos e aplicações para gerir a conta de automatização. |
| Contribuinte |A função de Contribuidor permite-lhe gerir tudo, exceto modificar as permissões de acesso de outro utilizador para uma conta de Automatização. |
| Leitor |A função de Leitor permite-lhe ver todos os recursos numa conta de Automatização, mas não permite efetuar quaisquer alterações. |
| Operador de Automatização |A função de operador de automatização permite-lhe ver o nome do runbook e as propriedades e criar e gerir tarefas de todos os runbooks numa conta de automatização. Esta função é útil se pretender proteger os seus recursos da Conta de Automatização como recursos de credenciais e runbooks contra a visualização ou modificação, mas ainda permite que os membros da sua organização executem estes runbooks. |
|Operador de Tarefas de Automatização|A função de operador de tarefas de automatização permite-lhe criar e gerir tarefas de todos os runbooks numa conta de automatização.|
|Operador de Runbook de Automatização|A função de operador de Runbook de automatização permite-lhe ver o nome e propriedades de um runbook.|
| Contribuidor do Log Analytics | A função de Contribuidor do Log Analytics permite-lhe ler todos os dados de monitorização e editar as definições de monitorização. Editar definições de monitorização inclui adicionar a extensão VM para VMs, ler as chaves de conta de armazenamento para poder configurar a recolha de registos a partir do armazenamento do Azure, criar e configurar contas de automatização, adicionar soluções e configurar os diagnósticos do Azure em todos os recursos do Azure.|
| Leitor do Log Analytics | A função de leitor do Log Analytics permite-lhe ver e procurar todas as monitorização dados, bem como ver definições de monitorização. Isto inclui a visualização da configuração dos diagnósticos do Azure em todos os recursos do Azure. |
| Contribuidor de Monitorização | A função de Contribuidor de monitorização permite-lhe ler todos os dados de monitorização e atualização de definições de monitorização.|
| Leitor de Monitorização | A função de leitor de monitorização permite-lhe ler todos os dados de monitorização. |
| Administrador de Acesso dos Utilizadores |A função de Administrador de Acesso de Utilizador permite-lhe gerir o acesso de utilizador para as contas de Automatização do Azure. |

## <a name="role-permissions"></a>Permissões de funções

As tabelas seguintes descrevem as permissões específicas para cada função. Isto pode incluir ações, que concede permissões, e NotActions, que restringi-los.

### <a name="owner"></a>Proprietário

Um proprietário pode gerir tudo, incluindo o acesso. A tabela seguinte mostra as permissões concedidas para a função:

|Ações|Descrição|
|---|---|
|Microsoft.Automation/automationAccounts/|Criar e gerir recursos de todos os tipos.|

### <a name="contributor"></a>Contribuinte

Contribuidor pode gerir tudo, exceto o acesso. A tabela seguinte mostra as permissões concedidas e negado para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Criar e gerir recursos de todos os tipos|
|**Ações de não**||
|Microsoft.Authorization/*/Delete| Elimine funções e as atribuições de funções.       |
|Microsoft.Authorization/*/Write     |  Crie funções e as atribuições de funções.       |
|Microsoft.Authorization/elevateAccess/Action    | Nega a capacidade de criar um administrador de acesso do utilizador.       |

### <a name="reader"></a>Leitor

Um leitor pode ver todos os recursos numa conta de automatização, mas não é possível fazer alterações.

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Ver todos os recursos numa conta de automatização. |

### <a name="automation-operator"></a>Operador de Automatização

Um operador de automatização é capaz de criar e gerir tarefas e leia os nomes de runbook e as propriedades de todos os runbooks numa conta de automatização.  Nota: Se pretender controlar o acesso de operador individuais para runbooks, em seguida, não defina esta função em vez disso, as funções de "Operador de tarefas de automatização" e "Operador de Runbook de automatização" em combinação. A tabela seguinte mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Authorization/*/read|Leia a autorização.|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|Leia os recursos de trabalho de Runbook híbrida.|
|Microsoft.Automation/automationAccounts/jobs/read|Lista de tarefas do runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Retome uma tarefa que está em pausa.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Cancele uma tarefa em curso.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Leia a fluxos de trabalho e a saída.|
|Microsoft.Automation/automationAccounts/jobs/output/read|Obtenha o resultado de uma tarefa.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Colocar em pausa uma tarefa em curso.|
|Microsoft.Automation/automationAccounts/jobs/write|Crie tarefas.|
|Microsoft.Automation/automationAccounts/jobSchedules/read|Obtenha um plano de trabalho de automatização do Azure.|
|Microsoft.Automation/automationAccounts/jobSchedules/write|Crie um plano de trabalho de automatização do Azure.|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|Obtenha a área de trabalho ligada à conta de automatização.|
|Microsoft.Automation/automationAccounts/read|Obtenha uma conta de automatização do Azure.|
|Microsoft.Automation/automationAccounts/runbooks/read|Obtenha um runbook da automatização do Azure.|
|Microsoft.Automation/automationAccounts/schedules/read|Obter um recurso de agenda da automatização do Azure.|
|Microsoft.Automation/automationAccounts/schedules/write|Criar ou atualizar um recurso de agenda da automatização do Azure.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Leia as funções e as atribuições de funções.         |
|Microsoft.Resources/deployments/*      |Criar e gerir implementações de grupo de recursos.         |
|Microsoft.Insights/alertRules/*      | Criar e gerir regras de alerta.        |
|Microsoft.Support/* |Criar e gerir pedidos de suporte.|

### <a name="automation-job-operator"></a>Operador de Tarefas de Automatização

Uma função de operador de tarefas de automatização é concedida no âmbito da conta de automatização. Isso permite que as permissões de operador para criar e gerir tarefas de todos os runbooks na conta. A tabela seguinte mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Authorization/*/read|Leia a autorização.|
|Microsoft.Automation/automationAccounts/jobs/read|Lista de tarefas do runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Retome uma tarefa que está em pausa.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Cancele uma tarefa em curso.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Leia a fluxos de trabalho e a saída.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Colocar em pausa uma tarefa em curso.|
|Microsoft.Automation/automationAccounts/jobs/write|Crie tarefas.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Leia as funções e as atribuições de funções.       |
|Microsoft.Resources/deployments/*      |Criar e gerir implementações de grupo de recursos.         |
|Microsoft.Insights/alertRules/*      | Criar e gerir regras de alerta.        |
|Microsoft.Support/* |Criar e gerir pedidos de suporte.|

### <a name="automation-runbook-operator"></a>Operador de Runbook de Automatização

Uma função de operador de Runbook de automatização é concedida no âmbito do Runbook. Um operador de Runbook de automatização pode ver o nome e as propriedades do runbook.  Esta função combinada com a função "Operador de tarefas de automatização" permite que o operador para também criar e gerir tarefas do runbook. A tabela seguinte mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Liste os runbooks.        |
|Microsoft.Authorization/*/read      | Leia a autorização.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Leia as funções e as atribuições de funções.         |
|Microsoft.Resources/deployments/*      | Criar e gerir implementações de grupo de recursos.         |
|Microsoft.Insights/alertRules/*      | Criar e gerir regras de alerta.        |
|Microsoft.Support/*      | Criar e gerir pedidos de suporte.        |

### <a name="log-analytics-contributor"></a>Contribuidor do Log Analytics

Contribuidor do Log Analytics pode ler todos os dados de monitorização e editar as definições de monitorização. Editar definições de monitorização inclui adicionar a extensão VM para VMs; ler as chaves de conta de armazenamento para poder configurar a recolha de registos do armazenamento do Azure; criar e configurar contas de automatização; Adicionar soluções; e configurar os diagnósticos do Azure em todos os recursos do Azure. A tabela seguinte mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|* / leitura|Ler os recursos de todos os tipos, exceto segredos.|
|Microsoft.Automation/automationAccounts/*|Gerir contas de automatização.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Criar e gerir extensões de máquina virtual.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Listar chaves de conta de armazenamento clássicas.|
|Microsoft.Compute/virtualMachines/extensions/*|Criar e gerir extensões de máquina virtual clássica.|
|Microsoft.Insights/alertRules/*|Regras de alerta de leitura/escrita/eliminar.|
|Microsoft.Insights/diagnosticSettings/*|Definições de diagnóstico de leitura/escrita/eliminar.|
|Microsoft.OperationalInsights/*|Gerir registos do Azure Monitor.|
|Microsoft.OperationsManagement/*|Gerir soluções em áreas de trabalho.|
|Microsoft.Resources/deployments/*|Criar e gerir implementações de grupo de recursos.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Criar e gerir implementações de grupo de recursos.|
|Microsoft.Storage/storageAccounts/listKeys/action|Listar chaves de conta de armazenamento.|
|Microsoft.Support/*|Criar e gerir pedidos de suporte.|

### <a name="log-analytics-reader"></a>Leitor do Log Analytics

Um leitor do Log Analytics pode ver e procurar todos os dados de monitorização e como e vista de definições, incluindo ver a configuração dos diagnósticos do Azure em todos os recursos do Azure de monitorização. A tabela seguinte mostra as permissões concedidas ou negadas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|* / leitura|Ler os recursos de todos os tipos, exceto segredos.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Gerir consultas nos registos do Azure Monitor.|
|Microsoft.OperationalInsights/workspaces/search/action|Procure dados de registo do Azure Monitor.|
|Microsoft.Support/*|Criar e gerir pedidos de suporte.|
|**Ações de não**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Não é possível ler as chaves de acesso partilhado.|

### <a name="monitoring-contributor"></a>Contribuidor de Monitorização

Contribuidor de monitorização pode ler todos os dados de monitorização e atualizar as definições de monitorização. A tabela seguinte mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|* / leitura|Ler os recursos de todos os tipos, exceto segredos.|
|Microsoft.AlertsManagement/alerts/*|Gerir alertas.|
|Microsoft.AlertsManagement/alertsSummary/*|Gerir o dashboard de alerta.|
|Microsoft.Insights/AlertRules/*|Gerir regras de alerta.|
|Microsoft.Insights/components/*|Gerir componentes do Application Insights.|
|Microsoft.Insights/DiagnosticSettings/*|Gerir definições de diagnóstico.|
|Microsoft.Insights/eventtypes/*|Lista de eventos de registo de Atividades (eventos de gestão) numa subscrição. Esta permissão é aplicável ao portal e programático acesso ao registo de atividades.|
|Microsoft.Insights/LogDefinitions/*|Esta permissão é necessária para os utilizadores que necessitam de aceder a registos de Atividades através do portal. Lista as categorias de registo no registo de atividades.|
|Microsoft.Insights/MetricDefinitions/*|Ler definições de métrica (lista de tipos de métricas disponíveis para um recurso).|
|Microsoft.Insights/Metrics/*|Ler métricas para um recurso.|
|Microsoft.Insights/Register/Action|Registe o fornecedor de Microsoft. insights.|
|Microsoft.Insights/webtests/*|Faça a gestão de testes web do Application Insights.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Gerir pacotes de solução de registos do Azure Monitor.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Gerir as pesquisas de registos guardados do Azure Monitor.|
|Microsoft.OperationalInsights/workspaces/search/action|Procure áreas de trabalho do Log Analytics.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Listar chaves para uma área de trabalho do Log Analytics.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Gerir configurações de informações de armazenamento de registos do Azure Monitor.|
|Microsoft.Support/*|Criar e gerir pedidos de suporte.|
|Microsoft.WorkloadMonitor/workloads/*|Gerir cargas de trabalho.|

### <a name="monitoring-reader"></a>Leitor de Monitorização

Um leitor de monitorização pode ler todos os dados de monitorização. A tabela seguinte mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|* / leitura|Ler os recursos de todos os tipos, exceto segredos.|
|Microsoft.OperationalInsights/workspaces/search/action|Procure áreas de trabalho do Log Analytics.|
|Microsoft.Support/*|Criar e gerir pedidos de suporte|

### <a name="user-access-administrator"></a>Administrador de Acesso dos Utilizadores

Um administrador de acesso de utilizador pode gerir o acesso dos utilizadores aos recursos do Azure. A tabela seguinte mostra as permissões concedidas para a função:

|**Ações**  |**Descrição**  |
|---------|---------|
|* / leitura|Ler todos os recursos|
|Microsoft.Authorization/*|Gerir a autorização|
|Microsoft.Support/*|Criar e gerir pedidos de suporte|

## <a name="onboarding"></a>Carregamento

As tabelas seguintes mostram as permissões necessárias mínimo necessárias para máquinas de virtuais de integração para o controlo de alterações ou atualizar soluções de gestão.

### <a name="onboarding-from-a-virtual-machine"></a>Inclusão de uma máquina virtual

|**ação**  |**Permissão**  |**Âmbito mínimo**  |
|---------|---------|---------|
|Escrever a nova implementação      | Microsoft.Resources/deployments/*          |Subscrição          |
|Escrever o novo grupo de recursos      | Microsoft.Resources/subscriptions/resourceGroups/write        | Subscrição          |
|Criar nova área de trabalho de predefinição      | Microsoft.OperationalInsights/workspaces/write         | Grupo de recursos         |
|Criar nova conta      |  Microsoft.Automation/automationAccounts/write        |Grupo de recursos         |
|Área de trabalho de ligação e a conta      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|Área de trabalho</br>Conta de automatização
|Criar solução      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write |Grupo de recursos          |
|Criar a extensão do MMA      | Microsoft.Compute/virtualMachines/write         | Virtual Machine         |
|Criar pesquisa guardada      | Microsoft.OperationalInsights/workspaces/write          | Área de trabalho         |
|Criar configuração de âmbito      | Microsoft.OperationalInsights/workspaces/write          | Área de trabalho         |
|Solução de ligação à configuração de âmbito      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Solução         |
|Verifique o estado de carregamento - área de trabalho de leitura      | Microsoft.OperationalInsights/workspaces/read         | Área de trabalho         |
|Verificação do Estado de inclusão - leitura ligada a propriedade de área de trabalho da conta     | Microsoft.Automation/automationAccounts/read      | Conta de automatização        |
|Verifique o estado de carregamento - solução de leitura      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Solução         |
|Verificação de estado de inclusão - VM de leitura      | Microsoft.Compute/virtualMachines/read         | Virtual Machine         |
|Verifique o estado de carregamento - conta de leitura      | Microsoft.Automation/automationAccounts/read  |  Conta de automatização   |

### <a name="onboarding-from-automation-account"></a>Integração da conta de automatização

|**ação**  |**Permissão** |**Âmbito mínimo**  |
|---------|---------|---------|
|Criar nova implementação     | Microsoft.Resources/deployments/*        | Subscrição         |
|Criar um novo grupo de recursos     | Microsoft.Resources/subscriptions/resourceGroups/write         | Subscrição        |
|Painel AutomationOnboarding - criar nova área de trabalho     |Microsoft.OperationalInsights/workspaces/write           | Grupo de recursos        |
|Painel de AutomationOnboarding - ler a área de trabalho ligada     | Microsoft.Automation/automationAccounts/read        | Conta de automatização       |
|Painel de AutomationOnboarding - solução de leitura     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Solução        |
|Painel de AutomationOnboarding - área de trabalho de leitura     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Área de trabalho        |
|Criar ligação para a área de trabalho e conta     | Microsoft.OperationalInsights/workspaces/write        | Área de trabalho        |
|Escrever conta para shoebox      | Microsoft.Automation/automationAccounts/write        | Conta        |
|Criar solução      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write        | Grupo de recursos         |
|Criar/editar pesquisa guardada     | Microsoft.OperationalInsights/workspaces/write        | Área de trabalho        |
|Criar/Editar configuração de âmbito     | Microsoft.OperationalInsights/workspaces/write        | Área de trabalho        |
|Solução de ligação à configuração de âmbito      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Solução         |
|**Passo 2 - carregar várias VMs**     |         |         |
|Painel VMOnboarding - extensão MMA criar     | Microsoft.Compute/virtualMachines/write           | Virtual Machine        |
|Criar / editar pesquisa guardada     | Microsoft.OperationalInsights/workspaces/write           | Área de trabalho        |
|Criar / Editar configuração de âmbito  | Microsoft.OperationalInsights/workspaces/write   | Área de trabalho|

## <a name="update-management"></a>Gestão de atualizações

Gestão de atualizações atinge em vários serviços para fornecer o respetivo serviço. A tabela seguinte mostra as permissões necessárias para gerir implementações de atualização de gestão:

|**Recurso**  |**Função**  |**Âmbito**  |
|---------|---------|---------|
|Conta de automatização     | Contribuidor do Log Analytics       | Conta de automatização        |
|Conta de automatização    | Contribuinte de Máquina Virtual        | Grupo de recursos para a conta        |
|Área de trabalho do Log Analytics     | Contribuidor do Log Analytics| Área de trabalho do Log Analytics        |
|Área de trabalho do Log Analytics |Leitor do Log Analytics| Subscrição|
|Solução     |Contribuidor do Log Analytics         | Solução|
|Virtual Machine     | Contribuinte de Máquina Virtual        | Virtual Machine        |

## <a name="configure-rbac-for-your-automation-account"></a>Configurar o RBAC para a sua conta de automatização

A seção a seguir mostra como configurar o RBAC na sua conta de automatização através da [portal](#configure-rbac-using-the-azure-portal) e [PowerShell](#configure-rbac-using-powershell)

### <a name="configure-rbac-using-the-azure-portal"></a>Configurar o RBAC com o portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) e abra a sua conta de Automatização a partir da página Contas de Automatização.
2. Clique nas **controlo de acesso (IAM)** controle no canto superior esquerdo. Esta ação abre o **controlo de acesso (IAM)** página onde pode adicionar novos utilizadores, grupos e aplicações para gerir a sua automação de conta e ver funções existentes que podem ser configuradas para a conta de automatização.
3. Clique nas **atribuições de funções** separador.

   ![Botão de acesso](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Adicionar um novo utilizador e atribuir uma função

1. Do **controlo de acesso (IAM)** página, clique em **+ adicionar atribuição de função** para abrir o **adicionar atribuição de função** página onde pode adicionar um utilizador, grupo ou aplicação e atribuir uma função a eles.

2. Selecione uma função na lista de funções disponíveis. Pode escolher qualquer uma das funções incorporadas disponíveis que suporte a uma conta de automatização ou qualquer função personalizada que pode ter definido.

3. Escreva o nome de utilizador do utilizador que pretende conceder permissões na **selecione** campo. Selecione o utilizador a partir da lista e clique em **guardar**.

   ![Adicionar utilizadores](media/automation-role-based-access-control/automation-04-add-users.png)

   Agora verá que o utilizador adicionado para o **utilizadores** página com a função selecionada atribuída

   ![Listar utilizadores](media/automation-role-based-access-control/automation-05-list-users.png)

   Também pode atribuir uma função ao utilizador a partir da página **Funções**.
4. Clique em **funções** partir a **controlo de acesso (IAM)** página para abrir o **funções** página. A partir daí, pode ver o nome da função, o número de utilizadores e os grupos atribuídos a essa função.

    ![Atribuir função a partir da página Utilizadores](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Controlo de acesso baseado em funções só pode ser definido no âmbito da conta de automatização e não em qualquer recurso abaixo a conta de automatização.

#### <a name="remove-a-user"></a>Remover um utilizador

Pode remover a permissão de acesso para um utilizador que não está a gerir a conta de automatização ou que já não trabalha para a organização. Seguem-se os passos para remover um utilizador:

1. Partir do **controlo de acesso (IAM)** , selecione a utilizador pretenda remover e clique em **remover**.
2. Clique no botão **Remover** na página de detalhes de atribuição.
3. Clique em **Sim** para confirmar a remoção.

   ![Remover utilizadores](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-rbac-using-powershell"></a>Configurar o RBAC com o PowerShell

Acesso baseado em funções também pode ser configurado para uma conta de automatização utilizando os seguintes [cmdlets do Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

[Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) apresenta uma lista de todas as funções RBAC que estão disponíveis no Azure Active Directory. Pode utilizar este comando juntamente com a propriedade **Nome** para listar todas as ações que podem ser utilizadas com uma função específica.

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

[Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) apresenta uma lista de atribuições de funções de RBAC do Azure AD no âmbito especificado. Sem quaisquer parâmetros, este comando devolve todas as atribuições de funções efetuadas sob a subscrição. Utilize o parâmetro **ExpandPrincipalGroups** para listar atribuições de acesso para o utilizador especificado, bem como para os grupos dos quais o utilizador é membro.
    **Exemplo:** Utilize o seguinte comando para listar todos os utilizadores e as respetivas funções dentro de uma conta de automatização.

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

[Novo-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) atribuir acesso a utilizadores, grupos e aplicações num determinado âmbito.
    **Exemplo:** Utilize o seguinte comando para atribuir a função "Operador de automatização" para um utilizador no âmbito da conta de automatização.

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

Uso [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) para remover o acesso de um utilizador especificado, grupo ou aplicação de um âmbito específico.
    **Exemplo:** Utilize o seguinte comando para remover o utilizador da função "Operador de automatização" no âmbito da conta de automatização.

```azurepowershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Nos exemplos anteriores, substitua **Id início de sessão**, **Id de subscrição**, **nome do grupo de recursos**, e **nome da conta de automatização** com sua Detalhes da conta. Escolha **Sim** quando lhe for solicitado para confirmar antes de continuar para remover a atribuição de função de utilizador.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Experiência de utilizador para a função de operador de automatização - conta de automatização

Quando um utilizador, quem é atribuído à função de operador de automatização no âmbito da conta de automatização vê a conta de automatização estão atribuídos, apenas podem visualizar a lista de runbooks, tarefas de runbook e agendas criadas na Automação de contas, mas não é possível ver seus definição. Pode iniciar, parar, suspender, retomar ou agendar a tarefa de runbook. O utilizador não tem acesso a outros recursos de automatização, como configurações, grupos de trabalho híbrida ou nós de DSC.

![Sem acesso aos recursos](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-rbac-for-runbooks"></a>Configurar o RBAC para Runbooks

A automatização do Azure permite-lhe atribuir o RBAC para runbooks específicos. Para tal, execute o seguinte script para adicionar um utilizador a um runbook específico. O script seguinte pode ser executada por um administrador de conta de automatização ou o administrador de inquilinos.

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

Uma vez for executada, peça ao utilizador que inicie sessão no portal do Azure e exibição **todos os recursos**. Na lista veem o Runbook terem sido adicionados como um **operador de Runbook de automatização** para.

![RBAC de Runbook no portal](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Experiência de utilizador para a função de operador de automatização - Runbook

Quando um utilizador, o que é atribuído a função de operador de automatização em vistas de âmbito de Runbook um Runbook que estão atribuídos, podem apenas iniciar o runbook e ver as tarefas de runbook.

![Apenas tem acesso ao iniciar](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Passos Seguintes

* Para obter informações sobre diferentes maneiras de configurar o RBAC para a Automatização do Azure, veja [Gerir o RBAC com o Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Para obter detalhes sobre diferentes formas de iniciar um runbook, consulte o artigo [Iniciar um runbook](automation-starting-a-runbook.md)
* Para obter informações sobre os vários tipos de runbook, veja [Tipos de runbook de Automatização do Azure](automation-runbook-types.md)

