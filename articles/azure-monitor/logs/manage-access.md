---
title: Gerir espaços de trabalho do Log Analytics em Azure Monitor | Microsoft Docs
description: Pode gerir o acesso aos dados armazenados num espaço de trabalho do Log Analytics no Azure Monitor utilizando permissões de recursos, espaço de trabalho ou nível de mesa. Este artigo detalha como completar.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/10/2019
ms.openlocfilehash: 184d5c98b2b434c87e112c569ff4e8ab347344c5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100613981"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>Gerir o acesso a dados de registo e áreas de trabalho no Azure Monitor

O Azure Monitor armazena [dados de registo](../logs/data-platform-logs.md) num espaço de trabalho do Log Analytics. Um espaço de trabalho é um recipiente que inclui informações de dados e configuração. Para gerir o acesso aos dados de registo, executa várias tarefas administrativas relacionadas com o seu espaço de trabalho.

Este artigo explica como gerir o acesso aos registos e administrar os espaços de trabalho que os contêm, incluindo como conceder acesso a: 

* O espaço de trabalho utilizando permissões de espaço de trabalho.
* Utilizadores que precisam de acesso a dados de registo de recursos específicos usando o controlo de acesso baseado em funções Azure (Azure RBAC) - também conhecido como [contexto de recursos](../logs/design-logs-deployment.md#access-mode)
* Utilizadores que precisam de acesso a registar dados numa tabela específica no espaço de trabalho usando o Azure RBAC.

Para compreender os conceitos de Logs em torno do Azure RBAC e estratégias de acesso, leia [a conceção da sua implementação de Registos de Monitor Azure](../logs/design-logs-deployment.md)

## <a name="configure-access-control-mode"></a>Configure o modo de controlo de acesso

Pode visualizar o [modo de controlo de acesso](../logs/design-logs-deployment.md) configurado num espaço de trabalho a partir do portal Azure ou com o Azure PowerShell.  Pode alterar esta definição utilizando um dos seguintes métodos suportados:

* Portal do Azure

* Azure PowerShell

* Modelo Azure Resource Manager

### <a name="from-the-azure-portal"></a>No portal do Azure

Pode ver o atual modo de controlo do espaço de trabalho na página **'Vista Geral'** para o espaço de trabalho no menu **do espaço de trabalho Log Analytics.**

![Ver modo de controlo de acesso ao espaço de trabalho](media/manage-access/view-access-control-mode.png)

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
1. No portal Azure, selecione Log Analytics > seu espaço de trabalho.

Pode alterar esta definição a partir da página **Propriedades** do espaço de trabalho. A alteração da definição será desativada se não tiver permissões para configurar o espaço de trabalho.

![Alterar o modo de acesso ao espaço de trabalho](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>Com o PowerShell

Utilize o seguinte comando para examinar o modo de controlo de acesso para todos os espaços de trabalho na subscrição:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

A saída deve assemelhar-se ao seguinte:

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

Um valor de `False` meios o espaço de trabalho é configurado com o modo de acesso ao contexto do espaço de trabalho.  Um valor de `True` meios o espaço de trabalho é configurado com o modo de acesso ao contexto de recursos.

> [!NOTE]
> Se um espaço de trabalho é devolvido sem um valor boolean e está em branco, isso também corresponde aos resultados de um `False` valor.
>

Utilize o seguinte script para definir o modo de controlo de acesso para um espaço de trabalho específico para a permissão de contexto de recursos:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Utilize o seguinte script para definir o modo de controlo de acesso para todos os espaços de trabalho na subscrição da permissão de contexto de recursos:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
}
```

### <a name="using-a-resource-manager-template"></a>Utilizar um modelo do Resource Manager

Para configurar o modo de acesso num modelo Azure Resource Manager, deslogeu a bandeira de recurso **enableLogAccessSingOnlyResourcePermissions** no espaço de trabalho para um dos seguintes valores.

* **falso**: Coloque o espaço de trabalho em permissões de contexto de espaço de trabalho. Esta é a definição padrão se a bandeira não estiver definida.
* **verdadeiro**: Desaprote o espaço de trabalho para permissões de contexto de recursos.

## <a name="manage-access-using-workspace-permissions"></a>Gerir o acesso usando permissões de espaço de trabalho

Cada espaço de trabalho pode ter várias contas associadas a ele, e cada conta pode ter acesso a vários espaços de trabalho. O acesso é gerido utilizando o [controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).

As atividades seguintes também necessitam de permissões do Azure:

|Ação |Permissões do Azure Necessárias |Notas |
|-------|-------------------------|------|
| Adicionar e remover soluções de monitorização | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Estas permissões têm de ser concedidas ao nível do grupo de recursos ou da subscrição. |
| Alterar o escalão de preço | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Ver dados nos mosaicos de solução *Backup* e *Site Recovery* | Administrador/Coadministrador | Acede aos recursos implementados com o modelo de implementação clássica |
| Criar um espaço de trabalho no portal do Azure | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| Ver propriedades básicas do espaço de trabalho e entrar na lâmina do espaço de trabalho no portal | `Microsoft.OperationalInsights/workspaces/read` ||
| Registos de consulta usando qualquer interface | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Aceda a todos os tipos de registos usando consultas | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Aceda a uma tabela de registos específica | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| Leia as chaves do espaço de trabalho para permitir o envio de registos para este espaço de trabalho | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Gerir o acesso usando permissões Azure

Para conceder acesso à área de trabalho do Log Analytics com permissões do Azure, siga os passos em [Utilize atribuições de funções para gerir o acesso aos recursos de subscrição do Azure](../../role-based-access-control/role-assignments-portal.md). Por exemplo, funções personalizadas, ver [funções personalizadas exemplo](#custom-role-examples)

A Azure tem duas funções de utilizador incorporadas para espaços de trabalho Log Analytics:

* Leitor do Log Analytics
* Contribuidor do Log Analytics

Os membros da função *Leitor do Log Analytics* podem:

* Ver e procurar todos os dados de monitorização
* Ver e monitorizar as definições, incluindo ver a configuração dos diagnósticos do Azure em todos os recursos do Azure.

O papel do Leitor de Log Analytics inclui as seguintes ações do Azure:

| Tipo    | Permissão | Descrição |
| ------- | ---------- | ----------- |
| Ação | `*/read`   | Capacidade de visualizar todos os recursos Azure e configuração de recursos. Inclui ver: <br> o estado da extensão da máquina virtual <br> a configuração dos diagnósticos do Azure nos recursos <br> Todas as propriedades e configurações de todos os recursos. <br> Para espaços de trabalho, permite que todas as permissões ilimitadas leiam as definições do espaço de trabalho e realizem consultas nos dados. Veja mais opções granulares acima. |
| Ação | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Preprecados, não há necessidade de atribuí-los aos utilizadores. |
| Ação | `Microsoft.OperationalInsights/workspaces/search/action` | Preprecados, não há necessidade de atribuí-los aos utilizadores. |
| Ação | `Microsoft.Support/*` | Capacidade para abrir pedidos de suporte |
|Ação Não | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Impede a leitura da chave do espaço de trabalho necessária para utilizar a API de recolha de dados e instalar agentes. Isto impede o utilizador de adicionar novos recursos ao espaço de trabalho |

Os membros da função *Contribuidor do Log Analytics* podem:

* Inclui todos os privilégios da *função Log Analytics Reader,* permitindo ao utilizador ler todos os dados de monitorização
* Criar e configurar contas de automação
* Adicionar e remover soluções de gestão

    > [!NOTE]
    > Para realizar com sucesso as duas últimas ações, esta permissão deve ser concedida ao nível do grupo de recursos ou da subscrição.

* Leia as chaves da conta de armazenamento
* Configure a recolha de registos da Azure Storage
* Editar as definições de monitorização para recursos do Azure, incluindo
  * Adicionar a extensão de VM a VMs
  * Configurar os diagnósticos do Azure em todos os recursos do Azure

> [!NOTE]
> Pode utilizar a capacidade de adicionar uma extensão de máquina virtual a máquinas virtuais para obter o controlo total das mesmas.

A função de Contribuinte Log Analytics inclui as seguintes ações do Azure:

| Permissão | Description |
| ---------- | ----------- |
| `*/read`     | Capacidade para ver todos os recursos e configuração dos mesmos. Inclui ver: <br> o estado da extensão da máquina virtual <br> a configuração dos diagnósticos do Azure nos recursos <br> Todas as propriedades e configurações de todos os recursos. <br> Para espaços de trabalho, permite que permissões completas e ilimitadas leiam a definição do espaço de trabalho e realizem consultas nos dados. Veja mais opções granulares acima. |
| `Microsoft.Automation/automationAccounts/*` | Capacidade para criar, e configurar contas de Automatização do Azure, incluindo adicionar e editar runbooks |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Adicionar, atualizar e remover extensões de máquinas virtuais, incluindo a extensão Microsoft Monitoring Agent e o Agente do OMS para a extensão do Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Ver a chave da conta de armazenamento. Necessária para configurar o Log Analytics para ler registos a partir das contas de Armazenamento do Azure |
| `Microsoft.Insights/alertRules/*` | Adicionar, atualizar e remover regras de alertas |
| `Microsoft.Insights/diagnosticSettings/*` | Adicionar, atualizar e remover as definições de diagnósticos em recursos do Azure |
| `Microsoft.OperationalInsights/*` | Adicione, atualize e remova a configuração para os espaços de trabalho do Log Analytics. Para editar definições avançadas do espaço de trabalho, o utilizador precisa `Microsoft.OperationalInsights/workspaces/write` . |
| `Microsoft.OperationsManagement/*` | Adicionar e remover soluções de gestão |
| `Microsoft.Resources/deployments/*` | Criar e eliminar as implementações. Necessário para adicionar e remover soluções, áreas de trabalho e contas de automatização |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Criar e eliminar as implementações. Necessário para adicionar e remover soluções, áreas de trabalho e contas de automatização |

Para adicionar e remover utilizadores de uma função de utilizador, é necessário ter as permissões `Microsoft.Authorization/*/Delete` e `Microsoft.Authorization/*/Write`.

Utilize estas funções para conceder aos utilizadores acesso em âmbitos diferentes:

* Subscrição - acesso a todas as áreas de trabalho da subscrição
* Grupo de Recursos - acesso a todas as áreas de trabalho no grupo de recursos
* Recurso - acesso apenas à área de trabalho especificada

Recomendamos a realização de atribuições ao nível dos recursos (espaço de trabalho) para garantir um controlo preciso do acesso. Utilize [funções personalizadas](../../role-based-access-control/custom-roles.md) para criar funções com as permissões específicas necessárias.

### <a name="resource-permissions"></a>Permissões de recursos

Quando os utilizadores consultam os registos de um espaço de trabalho utilizando o acesso ao contexto de recursos, terão as seguintes permissões no recurso:

| Permissão | Description |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Exemplos:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Capacidade de visualizar todos os dados de registo para o recurso.  |
| `Microsoft.Insights/diagnosticSettings/write` | Capacidade de configurar a definição de diagnósticos para permitir a configuração de registos para este recurso. |

`/read`a permissão é geralmente concedida a partir de uma função que inclui _\* /ler ou_ _\*_ permissões, tais como as funções de [Leitor](../../role-based-access-control/built-in-roles.md#reader) incorporado e [Contribuinte.](../../role-based-access-control/built-in-roles.md#contributor) Papéis personalizados que incluem ações específicas ou funções incorporadas dedicadas podem não incluir esta permissão.

Consulte [a definição do controlo de acesso por tabela](#table-level-azure-rbac) abaixo se pretender criar diferentes controlos de acesso para diferentes tabelas.

## <a name="custom-role-examples"></a>Exemplos de funções personalizadas

1. Para conceder a um utilizador acesso ao registo de dados a partir dos seus recursos, execute o seguinte:

    * Configure o modo de controlo do acesso ao espaço de trabalho para **utilizar permissões de espaço de trabalho ou recursos**

    * Conceda aos utilizadores `*/read` ou `Microsoft.Insights/logs/*/read` permissões aos seus recursos. Se já lhes for atribuído o papel [de Leitor de Log Analytics](../../role-based-access-control/built-in-roles.md#reader) no espaço de trabalho, é suficiente.

2. Para conceder a um utilizador acesso ao registo de dados a partir dos seus recursos e configurar os seus recursos para enviar registos para o espaço de trabalho, execute o seguinte:

    * Configure o modo de controlo do acesso ao espaço de trabalho para **utilizar permissões de espaço de trabalho ou recursos**

    * Conceda aos utilizadores as seguintes permissões no espaço de trabalho: `Microsoft.OperationalInsights/workspaces/read` e `Microsoft.OperationalInsights/workspaces/sharedKeys/action` . Com estas permissões, os utilizadores não podem realizar nenhuma consulta ao nível do espaço de trabalho. Só podem enumerar o espaço de trabalho e usá-lo como destino para configurações de diagnóstico ou configuração de agente.

    * Conceda aos utilizadores as seguintes permissões aos seus recursos: `Microsoft.Insights/logs/*/read` e `Microsoft.Insights/diagnosticSettings/write` . Se já lhes for atribuída a função [De Contribuinte Log Analytics,](../../role-based-access-control/built-in-roles.md#contributor) atribuída a função Reader ou `*/read` permissões nesse recurso, é suficiente.

3. Para conceder a um utilizador acesso ao registo de dados a partir dos seus recursos sem ser capaz de ler eventos de segurança e enviar dados, execute o seguinte:

    * Configure o modo de controlo do acesso ao espaço de trabalho para **utilizar permissões de espaço de trabalho ou recursos**

    * Conceda aos utilizadores as seguintes permissões aos seus recursos: `Microsoft.Insights/logs/*/read` .

    * Adicione o seguinte NonAction para impedir os utilizadores de ler o tipo SecurityEvent: `Microsoft.Insights/logs/SecurityEvent/read` . A Não-Aacção deve desempenhar o mesmo papel personalizado que a ação que forneça a permissão de leitura `Microsoft.Insights/logs/*/read` (). Se o utilizador inerente à ação de leitura a partir de outra função atribuída a este recurso ou ao grupo de subscrição ou recursos, poderá ler todos os tipos de registo. Isto também é verdade se herdam, `*/read` que existem, por exemplo, com o papel de Leitor ou Contribuinte.

4. Para conceder a um utilizador acesso ao registo de dados a partir dos seus recursos e ler todos os dados de login da AD Azure e ler os dados de registo de solução de Gestão de Atualização a partir do espaço de trabalho, execute os seguintes:

    * Configure o modo de controlo do acesso ao espaço de trabalho para **utilizar permissões de espaço de trabalho ou recursos**

    * Conceda aos utilizadores as seguintes permissões no espaço de trabalho: 

        * `Microsoft.OperationalInsights/workspaces/read` – necessário para que o utilizador possa enumerar o espaço de trabalho e abrir a lâmina do espaço de trabalho no portal Azure
        * `Microsoft.OperationalInsights/workspaces/query/read` – necessário para cada utilizador que possa executar consultas
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read` – ser capaz de ler registos de login Azure AD
        * `Microsoft.OperationalInsights/workspaces/query/Update/read` – ser capaz de ler registos de soluções de Gestão de Atualização
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read` – ser capaz de ler registos de soluções de Gestão de Atualização
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read` – ser capaz de ler registos de gestão de atualização
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read` – necessária para poder utilizar a solução de Gestão de Atualização
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read` – necessária para poder utilizar a solução de Gestão de Atualização

    * Conceda aos utilizadores as seguintes permissões aos seus recursos: `*/read` , atribuídos à função Reader, ou `Microsoft.Insights/logs/*/read` . 

## <a name="table-level-azure-rbac"></a>Nível de mesa Azure RBAC

**O nível de tabela Azure RBAC** permite-lhe definir mais controlo granular aos dados num espaço de trabalho log analytics, além das outras permissões. Este controlo permite definir tipos de dados específicos que são acessíveis apenas a um conjunto específico de utilizadores.

Implementa o controlo de acesso à mesa com [funções personalizadas Azure](../../role-based-access-control/custom-roles.md) para conceder acesso a [mesas específicas](../logs/data-platform-logs.md) no espaço de trabalho. Estas funções são aplicadas em espaços de trabalho com [modos](../logs/design-logs-deployment.md#access-control-mode) de controlo de acesso ao espaço de trabalho ou ao contexto de recursos, independentemente do modo de [acesso](../logs/design-logs-deployment.md#access-mode)do utilizador.

Crie uma [função personalizada](../../role-based-access-control/custom-roles.md) com as seguintes ações para definir o acesso ao controlo de acesso à mesa.

* Para conceder acesso a uma tabela, inclua-a na secção **Ações** da definição de função. Para subtrair o acesso **das Ações Permitidas,** inclua-o na secção **NotActions.**
* Utilize microsoft.OperationalInsights/workspaces/consulta/* para especificar todas as tabelas.

Por exemplo, para criar uma função com acesso às tabelas _Heartbeat_ e _AzureActivity,_ criar um papel personalizado utilizando as seguintes ações:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
    "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Para criar uma função com acesso apenas à tabela _SecurityBaseline,_ crie uma função personalizada utilizando as seguintes ações:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
],
```
Os exemplos acima definem uma lista de tabelas que são permitidas. Este exemplo mostra a definição de lista bloqueada quando um utilizador pode aceder a todas as tabelas, menos a tabela _SecurityAlert:_

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/*/read"
],
"notActions":  [
    "Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read"
],
```

### <a name="custom-logs"></a>Registos personalizados

 Os registos personalizados são criados a partir de fontes de dados, tais como registos personalizados e API de Colecionador de Dados HTTP. A forma mais fácil de identificar o tipo de registo é verificando as tabelas [listadas nos Registos Personalizados no esquema de registo](../log-query/log-analytics-tutorial.md#table-schema).

 Não pode conceder acesso a registos personalizados individuais, mas pode conceder acesso a todos os registos personalizados. Para criar uma função com acesso a todos os registos personalizados, crie um papel personalizado utilizando as seguintes ações:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
],
```
Uma abordagem alternativa para gerir o acesso a registos personalizados é atribuí-los a um recurso Azure e gerir o acesso usando o paradigma de contexto de recursos. Para utilizar este método, deve incluir o ID de recurso especificando-o no cabeçalho [x-ms-AzureResourceId](../logs/data-collector-api.md#request-headers) quando os dados são ingeridos para Registar aNalítico através da [API do Colecionador de Dados HTTP](../logs/data-collector-api.md). O ID do recurso deve ser válido e ter regras de acesso aplicadas a ele. Depois de ingeridos os registos, são acessíveis a quem tem acesso lido ao recurso, como explicado aqui.

Por vezes, os registos personalizados provêm de fontes que não estão diretamente associadas a um recurso específico. Neste caso, crie um grupo de recursos apenas para gerir o acesso a estes registos. O grupo de recursos não incorre em qualquer custo, mas dá-lhe um ID de recurso válido para controlar o acesso aos registos personalizados. Por exemplo, se uma firewall específica estiver a enviar registos personalizados, crie um grupo de recursos chamado "MyFireWallLogs" e certifique-se de que os pedidos da API contêm o ID de recursos de "MyFireWallLogs". Os registos de registos de firewall são então acessíveis apenas a utilizadores que tenham acesso a MyFireWallLogs ou aqueles com acesso total ao espaço de trabalho.          

### <a name="considerations"></a>Considerações

* Se um utilizador tiver permissão de leitura global com as funções padrão reader ou contribuinte que incluem a ação _\* /ler,_ irá sobrepor-se ao controlo de acesso por tabela e dar-lhes acesso a todos os dados de registo.
* Se um utilizador tiver acesso por tabela, mas sem outras permissões, poderá aceder aos dados de registo da API, mas não a partir do portal Azure. Para fornecer acesso a partir do portal Azure, utilize o Log Analytics Reader como função base.
* Os administradores e proprietários da subscrição terão acesso a todos os tipos de dados, independentemente de quaisquer outras definições de permissão.
* Os proprietários do espaço de trabalho são tratados como qualquer outro utilizador para o controlo de acesso por mesa.
* Recomendamos atribuir funções a grupos de segurança em vez de utilizadores individuais para reduzir o número de atribuições. Isto também o ajudará a usar as ferramentas de gestão do grupo existentes para configurar e verificar o acesso.

## <a name="next-steps"></a>Passos seguintes

* Consulte [a visão geral](../agents/log-analytics-agent.md) do agente Do Log Analytics para recolher dados de computadores no seu datacenter ou noutro ambiente em nuvem.

* Consulte [recolher dados sobre máquinas virtuais Azure](../learn/quick-collect-azurevm.md) para configurar a recolha de dados a partir de VMs Azure.