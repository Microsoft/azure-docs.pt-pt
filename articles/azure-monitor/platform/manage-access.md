---
title: Gerir espaços de trabalho de Log Analytics no Monitor Azure [ Monitor] Microsoft Docs
description: Pode gerir o acesso aos dados armazenados num espaço de trabalho do Log Analytics no Monitor Azure utilizando permissões de recursos, espaço de trabalho ou nível de mesa. Este artigo detalha como completar.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/10/2019
ms.openlocfilehash: 1e86317999a34e4ab4cb94f93fb788e3e7314cea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82193059"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>Gerir acesso a dados de log e espaços de trabalho no Monitor Azure

O Azure Monitor armazena dados de [registo](data-platform-logs.md) num espaço de trabalho do Log Analytics. Um espaço de trabalho é um recipiente que inclui dados e informações de configuração. Para gerir o acesso aos dados de registo, executa várias tarefas administrativas relacionadas com o seu espaço de trabalho.

Este artigo explica como gerir o acesso aos registos e administrar os espaços de trabalho que os contêm, incluindo como conceder acesso a: 

* O espaço de trabalho utilizando permissões no espaço de trabalho.
* Os utilizadores que precisam de acesso a dados de registo de recursos específicos utilizando o controlo de acesso baseado em funções do Azure (RBAC).
* Os utilizadores que precisam de acesso a dados de login numa tabela específica no espaço de trabalho utilizando o Azure RBAC.

## <a name="configure-access-control-mode"></a>Configurar o modo de controlo de acesso

Pode visualizar o modo de controlo de [acesso](design-logs-deployment.md) configurado num espaço de trabalho a partir do portal Azure ou com o Azure PowerShell.  Pode alterar esta definição utilizando um dos seguintes métodos suportados:

* Portal do Azure

* Azure PowerShell

* Modelo Azure Resource Manager

### <a name="from-the-azure-portal"></a>No portal do Azure

Pode visualizar o atual modo de controlo de acesso ao espaço de trabalho na página **'Overview'** para o espaço de trabalho no menu espaço de **trabalho Log Analytics.**

![Ver modo de controlo de acesso ao espaço de trabalho](media/manage-access/view-access-control-mode.png)

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
1. No portal Azure, selecione espaços de trabalho Log Analytics > seu espaço de trabalho.

Pode alterar esta definição a partir da página **Propriedades** do espaço de trabalho. A alteração da definição será desativada se não tiver permissão para configurar o espaço de trabalho.

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

Um valor `False` de meios de modo o espaço de trabalho está configurado com o modo de acesso ao contexto do espaço de trabalho.  Um valor `True` de meios de utilização o espaço de trabalho está configurado com o modo de acesso ao contexto de recursos.

> [!NOTE]
> Se um espaço de trabalho for devolvido sem um valor booleano e estiver em branco, este também corresponde aos resultados de um `False` valor.
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

Para configurar o modo de acesso num modelo de Gestor de Recursos Azure, detete a bandeira de funcionalidades **de funcionalidadeLogAccessUseOnlyResourcePermissions** no espaço de trabalho para um dos seguintes valores.

* **falso**: Desloque o espaço de trabalho para permissões de contexto de espaço de trabalho. Esta é a definição padrão se a bandeira não estiver definida.
* **verdadeiro**: Desloque o espaço de trabalho para permissões de contexto de recursos.

## <a name="manage-access-using-workspace-permissions"></a>Gerir o acesso usando permissões do espaço de trabalho

Cada espaço de trabalho pode ter várias contas associadas a ele, e cada conta pode ter acesso a vários espaços de trabalho. O acesso é gerido utilizando [o acesso baseado em funções azure.](../../role-based-access-control/role-assignments-portal.md)

As atividades seguintes também necessitam de permissões do Azure:

|Ação |Permissões do Azure Necessárias |Notas |
|-------|-------------------------|------|
| Adicionar e remover soluções de monitorização | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Estas permissões têm de ser concedidas ao nível do grupo de recursos ou da subscrição. |
| Alterar o escalão de preço | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Ver dados nos mosaicos de solução *Backup* e *Site Recovery* | Administrador/Coadministrador | Acede aos recursos implementados com o modelo de implementação clássica |
| Criar um espaço de trabalho no portal do Azure | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| Ver propriedades básicas do espaço de trabalho e entrar na lâmina do espaço de trabalho no portal | `Microsoft.OperationalInsights/workspaces/read` ||
| Registos de consulta usando qualquer interface | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Aceda a todos os tipos de registo usando consultas | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Aceda a uma tabela de registos específica | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| Leia as teclas do espaço de trabalho para permitir o envio de registos para este espaço de trabalho | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Gerir o acesso usando permissões Azure

Para conceder acesso à área de trabalho do Log Analytics com permissões do Azure, siga os passos em [Utilize atribuições de funções para gerir o acesso aos recursos de subscrição do Azure](../../role-based-access-control/role-assignments-portal.md). Por exemplo, papéis personalizados, consulte [funções personalizadas exemplo](#custom-role-examples)

O Azure tem duas funções de utilizador incorporadas para espaços de trabalho log Analytics:

* Leitor do Log Analytics
* Contribuidor do Log Analytics

Os membros da função *Leitor do Log Analytics* podem:

* Ver e procurar todos os dados de monitorização
* Ver e monitorizar as definições, incluindo ver a configuração dos diagnósticos do Azure em todos os recursos do Azure.

O papel do Log Analytics Reader inclui as seguintes ações do Azure:

| Tipo    | Permissão | Descrição |
| ------- | ---------- | ----------- |
| Ação | `*/read`   | Capacidade de visualizar todos os recursos do Azure e configuração de recursos. Inclui ver: <br> o estado da extensão da máquina virtual <br> a configuração dos diagnósticos do Azure nos recursos <br> Todas as propriedades e configurações de todos os recursos. <br> Para espaços de trabalho, permite permissões completas e sem restrições para ler as definições do espaço de trabalho e realizar consultas nos dados. Consulte mais opções granulares acima. |
| Ação | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Depreciado, não precisa atribuí-los aos utilizadores. |
| Ação | `Microsoft.OperationalInsights/workspaces/search/action` | Depreciado, não precisa atribuí-los aos utilizadores. |
| Ação | `Microsoft.Support/*` | Capacidade para abrir pedidos de suporte |
|Ação Não | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Impede a leitura da chave espaço de trabalho necessária para utilizar a API de recolha de dados e instalar agentes. Isto impede o utilizador de adicionar novos recursos ao espaço de trabalho |

Os membros da função *Contribuidor do Log Analytics* podem:

* Inclui todos os privilégios da *função Log Analytics Reader,* permitindo ao utilizador ler todos os dados de monitorização
* Criar e configurar contas de Automação
* Adicionar e remover soluções de gestão

    > [!NOTE]
    > Para realizar com sucesso as duas últimas ações, esta permissão tem de ser concedida ao nível do grupo de recursos ou de subscrição.

* Ler chaves da conta de armazenamento
* Configure a coleção de registos do Armazenamento Azure
* Editar as definições de monitorização para recursos do Azure, incluindo
  * Adicionar a extensão de VM a VMs
  * Configurar os diagnósticos do Azure em todos os recursos do Azure

> [!NOTE]
> Pode utilizar a capacidade de adicionar uma extensão de máquina virtual a máquinas virtuais para obter o controlo total das mesmas.

A função log analytics colaborador inclui as seguintes ações Azure:

| Permissão | Descrição |
| ---------- | ----------- |
| `*/read`     | Capacidade para ver todos os recursos e configuração dos mesmos. Inclui ver: <br> o estado da extensão da máquina virtual <br> a configuração dos diagnósticos do Azure nos recursos <br> Todas as propriedades e configurações de todos os recursos. <br> Para espaços de trabalho, permite permissões completas e sem restrições para ler a definição do espaço de trabalho e realizar consulta nos dados. Consulte mais opções granulares acima. |
| `Microsoft.Automation/automationAccounts/*` | Capacidade para criar, e configurar contas de Automatização do Azure, incluindo adicionar e editar runbooks |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Adicionar, atualizar e remover extensões de máquinas virtuais, incluindo a extensão Microsoft Monitoring Agent e o Agente do OMS para a extensão do Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Ver a chave da conta de armazenamento. Necessária para configurar o Log Analytics para ler registos a partir das contas de Armazenamento do Azure |
| `Microsoft.Insights/alertRules/*` | Adicionar, atualizar e remover regras de alertas |
| `Microsoft.Insights/diagnosticSettings/*` | Adicionar, atualizar e remover as definições de diagnósticos em recursos do Azure |
| `Microsoft.OperationalInsights/*` | Adicione, atualize e remova a configuração para espaços de trabalho de Log Analytics. Para editar definições avançadas `Microsoft.OperationalInsights/workspaces/write`do espaço de trabalho, o utilizador precisa . |
| `Microsoft.OperationsManagement/*` | Adicionar e remover soluções de gestão |
| `Microsoft.Resources/deployments/*` | Criar e eliminar as implementações. Necessário para adicionar e remover soluções, áreas de trabalho e contas de automatização |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Criar e eliminar as implementações. Necessário para adicionar e remover soluções, áreas de trabalho e contas de automatização |

Para adicionar e remover utilizadores de uma função de utilizador, é necessário ter as permissões `Microsoft.Authorization/*/Delete` e `Microsoft.Authorization/*/Write`.

Utilize estas funções para conceder aos utilizadores acesso em âmbitos diferentes:

* Subscrição - acesso a todas as áreas de trabalho da subscrição
* Grupo de Recursos - acesso a todas as áreas de trabalho no grupo de recursos
* Recurso - acesso apenas à área de trabalho especificada

Recomendamos a realização de tarefas ao nível dos recursos (espaço de trabalho) para garantir um controlo de acesso preciso. Utilize [funções personalizadas](../../role-based-access-control/custom-roles.md) para criar funções com as permissões específicas necessárias.

### <a name="resource-permissions"></a>Permissões de recursos

Quando os utilizadores consultam os registos de um espaço de trabalho utilizando o acesso ao contexto de recursos, terão as seguintes permissões no recurso:

| Permissão | Descrição |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Exemplos:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Capacidade de visualizar todos os dados de registo para o recurso.  |
| `Microsoft.Insights/diagnosticSettings/write` | Capacidade de configurar a definição de diagnósticopara permitir a configuração de registos para este recurso. |

`/read`a permissão é geralmente concedida a partir de uma função que inclui _\*_ _ \*/read ou_ permissões tais como as funções de [Leitor](../../role-based-access-control/built-in-roles.md#reader) incorporado e [Contribuinte.](../../role-based-access-control/built-in-roles.md#contributor) Funções personalizadas que incluam ações específicas ou funções dedicadas em butinso podem não incluir esta permissão.

Consulte [a Definição](#table-level-rbac) do controlo de acesso por mesa abaixo se pretender criar um controlo de acesso diferente para diferentes tabelas.

## <a name="custom-role-examples"></a>Exemplos de papéis personalizados

1. Para conceder ao utilizador acesso a dados de registo a partir dos seus recursos, execute o seguinte:

    * Configure o modo de controlo de acesso ao espaço de trabalho para **utilizar permissões** de espaço de trabalho ou recursos

    * Conceda `*/read` `Microsoft.Insights/logs/*/read` aos utilizadores ou permissões aos seus recursos. Se já lhes for atribuída a função de Leitor de [Log Analytics](../../role-based-access-control/built-in-roles.md#reader) no espaço de trabalho, é suficiente.

2. Para conceder ao utilizador acesso ao registo de dados a partir dos seus recursos e configurar os seus recursos para enviar registos para o espaço de trabalho, execute o seguinte:

    * Configure o modo de controlo de acesso ao espaço de trabalho para **utilizar permissões** de espaço de trabalho ou recursos

    * Conceda aos utilizadores as seguintes `Microsoft.OperationalInsights/workspaces/read` `Microsoft.OperationalInsights/workspaces/sharedKeys/action`permissões no espaço de trabalho: e . Com estas permissões, os utilizadores não podem realizar quaisquer consultas ao nível do espaço de trabalho. Só podem enumerar o espaço de trabalho e usá-lo como destino para configurações de diagnóstico ou configuração de agente.

    * Conceda aos utilizadores as seguintes permissões aos seus recursos: `Microsoft.Insights/logs/*/read` e `Microsoft.Insights/diagnosticSettings/write`. Se já lhes for atribuída a função de Colaborador de `*/read` [Log Analytics,](../../role-based-access-control/built-in-roles.md#contributor) atribuída a função de Leitor, ou autorizadas neste recurso, é suficiente.

3. Para conceder ao utilizador o acesso aos dados de registo dos seus recursos sem poder ler eventos de segurança e enviar dados, execute o seguinte:

    * Configure o modo de controlo de acesso ao espaço de trabalho para **utilizar permissões** de espaço de trabalho ou recursos

    * Conceda aos utilizadores as seguintes permissões aos seus recursos: `Microsoft.Insights/logs/*/read`.

    * Adicione o seguinte NonAction para impedir os `Microsoft.Insights/logs/SecurityEvent/read`utilizadores de lerem o tipo SecurityEvent: . A Não Ação deve ter o mesmo papel personalizado que`Microsoft.Insights/logs/*/read`a ação que forneça a permissão de leitura ( ). Se o utilizador inerente à ação de leitura de outra função que é atribuída a este recurso ou ao grupo de subscrição ou recursos, eles seriam capazes de ler todos os tipos de registo. Isto também é verdade `*/read`se herdarem, que existem, por exemplo, com o papel de Leitor ou Colaborador.

4. Para conceder a um utilizador o acesso ao registo de dados a partir dos seus recursos e ler todos os dados de log de solução de Atualização ad's do espaço de trabalho, execute o seguinte:

    * Configure o modo de controlo de acesso ao espaço de trabalho para **utilizar permissões** de espaço de trabalho ou recursos

    * Conceda aos utilizadores as seguintes permissões no espaço de trabalho: 

        * `Microsoft.OperationalInsights/workspaces/read`– necessário para que o utilizador possa enumerar o espaço de trabalho e abrir a lâmina do espaço de trabalho no portal Azure
        * `Microsoft.OperationalInsights/workspaces/query/read`– necessário para cada utilizador que possa executar consultas
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read`– poder ler registos de entrada da AD Azure
        * `Microsoft.OperationalInsights/workspaces/query/Update/read`– para poder ler registos de soluções de Gestão de Atualização
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read`– para poder ler registos de soluções de Gestão de Atualização
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read`– para poder ler registos de gestão de atualização
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read`– necessário para poder utilizar a solução de Gestão de Atualização
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read`– necessário para poder utilizar a solução de Gestão de Atualização

    * Conceda aos utilizadores as seguintes permissões aos seus recursos: `*/read`, atribuídas à função Reader, ou `Microsoft.Insights/logs/*/read`. 

## <a name="table-level-rbac"></a>RBAC nível de mesa

**O RBAC de nível de tabela** permite-lhe definir mais controlo granular aos dados num espaço de trabalho de Log Analytics, além das outras permissões. Este controlo permite definir tipos de dados específicos que são acessíveis apenas a um conjunto específico de utilizadores.

Implementa o controlo de acesso à mesa com [funções personalizadas](../../role-based-access-control/custom-roles.md) do Azure para garantir o acesso a [tabelas específicas](../log-query/logs-structure.md) no espaço de trabalho. Estas funções são aplicadas a espaços de trabalho com [modos](design-logs-deployment.md#access-control-mode) de controlo de acesso de contexto de espaço de trabalho ou de contexto de recursos, independentemente do modo de [acesso](design-logs-deployment.md#access-mode)do utilizador.

Crie um [papel personalizado](../../role-based-access-control/custom-roles.md) com as seguintes ações para definir o acesso ao controlo de acesso à mesa.

* Para conceder acesso a uma tabela, inclua-a na secção **ações** da definição de papel. Para subtrair o acesso das **Ações**permitidas, inclua-o na secção **NotActions.**
* Utilize microsoft.OperationalInsights/workspaces/consulta/* para especificar todas as tabelas.

Por exemplo, para criar um papel com acesso às tabelas _Heartbeat_ e _AzureActivity,_ criar uma função personalizada usando as seguintes ações:

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

### <a name="custom-logs"></a>Registos personalizados

 Os registos personalizados são criados a partir de fontes de dados, tais como registos personalizados e API de Colecionador de Dados HTTP. A forma mais fácil de identificar o tipo de registo é verificando as tabelas listadas [em Registos Personalizados no esquema](../log-query/get-started-portal.md#understand-the-schema)de log .

 Não pode conceder acesso a registos personalizados individuais, mas pode conceder acesso a todos os registos personalizados. Para criar um papel com acesso a todos os registos personalizados, crie uma função personalizada usando as seguintes ações:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
],
```
Uma abordagem alternativa para gerir o acesso a registos personalizados é atribuí-los a um recurso Azure e gerir o acesso usando o paradigma de contexto de recursos. Para utilizar este método, deve incluir o ID de recurso especificando-o no cabeçalho [x-ms-AzureResourceId](data-collector-api.md#request-headers) quando os dados são ingeridos para log Analytics através da [Http Data Collector API](data-collector-api.md). O ID de recurso deve ser válido e ter regras de acesso aplicadas. Depois de ingeridos os registos, são acessíveis a quem tem acesso lido ao recurso, como aqui explicado.

Por vezes, os registos personalizados provêm de fontes que não estão diretamente associadas a um recurso específico. Neste caso, crie um grupo de recursos apenas para gerir o acesso a estes registos. O grupo de recursos não incorre em qualquer custo, mas dá-lhe um ID de recurso válido para controlar o acesso aos registos personalizados. Por exemplo, se uma firewall específica estiver a enviar registos personalizados, crie um grupo de recursos chamado "MyFireWallLogs" e certifique-se de que os pedidos da API contêm o ID de recurso de "MyFireWallLogs". Os registos de registo de firewall são então acessíveis apenas a utilizadores a quem foi concedido acesso a MyFireWallLogs ou a pessoas com acesso total ao espaço de trabalho.          

### <a name="considerations"></a>Considerações

* Se um utilizador tiver autorização de leitura global com _ \*_ as funções padrão de Leitor ou Contribuinte que incluam a ação /ler, irá anular o controlo de acesso por mesa e dar-lhes acesso a todos os dados de registo.
* Se um utilizador tiver acesso por mesa, mas sem outras permissões, poderá aceder aos dados de registo da API, mas não do portal Azure. Para fornecer acesso ao portal Azure, utilize o Log Analytics Reader como a sua função base.
* Os administradores da subscrição terão acesso a todos os tipos de dados, independentemente de quaisquer outras definições de permissão.
* Os proprietários de espaço de trabalho são tratados como qualquer outro utilizador para controlo de acesso por mesa.
* Recomendamos a atribuição de funções a grupos de segurança em vez de utilizadores individuais para reduzir o número de atribuições. Isto também irá ajudá-lo a usar as ferramentas de gestão de grupo existentes para configurar e verificar o acesso.

## <a name="next-steps"></a>Passos seguintes

* Consulte a visão geral do [agente Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) para recolher dados de computadores no seu datacenter ou outro ambiente na nuvem.

* Consulte [a Recolha de dados sobre máquinas virtuais Azure](../../azure-monitor/learn/quick-collect-azurevm.md) para configurar a recolha de dados a partir de VMs Azure.
