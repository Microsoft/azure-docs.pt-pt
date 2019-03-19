---
title: Gerir áreas de trabalho do Log Analytics no Azure Monitor | Documentos da Microsoft
description: Pode gerir áreas de trabalho do Log Analytics no Azure Monitor com uma variedade de tarefas administrativas em utilizadores, contas, áreas de trabalho e contas do Azure.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: magoedte
ms.openlocfilehash: 66cef8369a314a76cf619e436cd25d6bdf628c45
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076314"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>Gerir dados de registo e áreas de trabalho no Azure Monitor
Arquivos de Monitor do Azure registos de dados numa área de trabalho do Log Analytics, que é essencialmente um contentor que inclui informações de configuração e dados. Para gerir o acesso aos registos de dados, realizar diversas tarefas administrativas relacionadas com a áreas de trabalho. O utilizador ou outros membros da sua organização podem utilizar várias áreas de trabalho para gerir diferentes conjuntos de dados recolhidos da totalidade ou de partes da sua infraestrutura de TI.

Este artigo explica como gerir o acesso aos logs e administrar as áreas de trabalho que os contêm. 

## <a name="create-a-workspace"></a>Criar uma área de trabalho
Para criar uma área de trabalho do Log Analytics, terá de:

1. Ter uma subscrição do Azure.
2. Escolher um nome de área de trabalho.
3. Associe a área de trabalho uma das suas subscrições e grupos de recursos.
4. Escolher uma localização geográfica.

Veja os artigos seguintes para obter detalhes sobre como criar uma área de trabalho:

- [Criar uma área de trabalho do Log Analytics no portal do Azure](../learn/quick-create-workspace.md)
- [Criar uma área de trabalho do Log Analytics com a CLI 2.0 do Azure](../learn/quick-create-workspace-cli.md)
- [Criar uma área de trabalho do Log Analytics com o Azure PowerShell](../learn/quick-create-workspace-posh.md)

## <a name="determine-the-number-of-workspaces-you-need"></a>Determinar o número de áreas de trabalho necessárias
Uma área de trabalho do Log Analytics é um recurso do Azure e é um contentor onde os dados é recolhidos, agregados, analisados e apresentados no Azure Monitor. Pode ter várias áreas de trabalho por subscrição do Azure e pode ter acesso a mais do que uma área de trabalho, com a capacidade de facilmente consultas em-los. Esta secção descreve quando pode ser útil criar mais do que uma área de trabalho.

Uma área de trabalho do Log Analytics fornece:

* Uma localização geográfica para armazenamento de dados.
* Isolamento de dados para definir os direitos de acesso de utilizador diferente no modo voltada para a área de trabalho. Não é relevante ao trabalhar no modo centrada no recurso.
* Como o âmbito para a configuração das definições [escalão de preço](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [retenção](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) e [dados capping](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap).
* Custos relacionados com a ingestão de dados e de retenção são feitos no recurso da área de trabalho.

Do ponto de vista para consumo, é recomendável que criar áreas de trabalho mínimo possível. Faz administração e a experiência de consulta mais fácil e rápido. Mas, com base nas características anteriores, talvez queira criar várias áreas de trabalho se:

* For uma empresa global e precisar de dados armazenados em regiões específicas por motivos de soberania ou conformidade dos dados de log.
* Estiver a utilizar o Azure e pretender evitar custos de transferência de dados de saída ao ter uma área de trabalho na mesma região dos recursos do Azure que gere.
* É um fornecedor de serviços geridos e necessita de manter os dados do Log Analytics de cada cliente gerido isolado em relação aos de outros clientes.
* Gerir vários clientes e pretender que cada cliente / departamento ou grupo empresarial veja os seus próprios dados, mas não os dados de outras pessoas, e não há nenhuma necessidade comercial de um cliente cruzada consolidada / departamento ou vista de grupo empresarial. ".

Quando utilizar agentes do Windows para recolher dados, pode [configurar cada agente para comunicar a uma ou mais áreas de trabalho](../../azure-monitor/platform/agent-windows.md).

Se estiver a utilizar o System Center Operations Manager, cada grupo de gestão do Operations Manager só pode ser ligado a uma área de trabalho. Pode instalar o Microsoft Monitoring Agent em computadores geridos pelo Operations Manager e fazer com que o agente reporte ao Operations Manager e a uma área de trabalho do Log Analytics diferente.

A arquitetura da área de trabalho definida, deve impor para esta política em recursos do Azure com [do Azure Policy](../../governance/policy/overview.md). Isto pode fornecer uma definição incorporada que seria automaticamente se aplicam a todos os recursos do Azure. Por exemplo, pode definir uma política para garantir que todos os seus recursos do Azure numa região específica enviado todos os seus registos de diagnóstico para uma área de trabalho específica.

## <a name="view-workspace-details"></a>Ver detalhes da área de trabalho
Enquanto analisa os dados na sua área de trabalho do Log Analytics do **do Azure Monitor** menu no portal do Azure, criar e gerir áreas de trabalho a **áreas de trabalho do Log Analytics** menu.
 

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e clique em **todos os serviços**. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **do Log Analytics** áreas de trabalho.  

    ![Portal do Azure](media/manage-access/azure-portal-01.png)  

3. Selecione a área de trabalho da lista.

4. A página de área de trabalho apresenta detalhes sobre a área de trabalho, introdução, configuração e ligações para informações adicionais.  

    ![Detalhes da área de trabalho](./media/manage-access/workspace-overview-page.png)  


## <a name="workspace-permissions-and-scope"></a>Permissões de área de trabalho e âmbito
Os dados que um utilizador tem acesso ao são determinados por vários fatores que estão listados na tabela seguinte. Cada um é descrito nas secções abaixo.

| Fator | Descrição |
|:---|:---|
| [Modo de acesso](#access-modes) | Método que o utilizador utiliza para acessa a área de trabalho.  Define o âmbito dos dados disponíveis e o modo de controlo de acesso que é aplicado. |
| [Modo de controlo de acesso](#access-control-mode) | Definir a área de trabalho que define se as permissões são aplicadas ao nível da área de trabalho ou recurso. |
| [Permissões](#manage-accounts-and-users) | Permissões aplicadas a pessoa ou grupos de utilizadores para a área de trabalho ou recurso. Define os dados que o utilizador terá acesso a. |
| [Nível de tabela RBAC](#table-level-rbac) | Permissões granulares opcionais que se aplica a todos os utilizadores, independentemente do respetivo modo de acesso ou o modo de controlo de acesso. Define os tipos de dados um utilizador pode aceder. |



## <a name="access-modes"></a>Modos de acesso
O _modo de acesso_ se refere à forma como um utilizador acede a uma área de trabalho do Log Analytics e define o âmbito dos dados podem aceder. 

**Área de trabalho centrado**: Neste modo, um utilizador pode ver todos os registos na área de trabalho que têm permissões para. Consultas neste modo estão confinadas a todos os dados em todas as tabelas na área de trabalho. Este é o modo de acesso utilizado quando os registos são acedidos com a área de trabalho como o escopo, por exemplo, quando seleciona **registos** partir a **Azure Monitor** menu no portal do Azure.

**Recurso centrado**: Quando acessar a área de trabalho para um recurso específico, por exemplo, quando seleciona **registos** de um menu de recursos no portal do Azure, pode ver os registos para apenas esse recurso em todas as tabelas que têm acesso a. Consultas neste modo estão confinadas a apenas os dados associados a esse recurso. Este modo também permite que o controlo de acesso granular baseada em funções (RBAC). 

> [!NOTE]
> Os registos estão disponíveis para consultas centrada em recursos apenas se eles foram corretamente associados com o recurso relevante. Atualmente, os seguintes recursos têm limitações: 
> - Computadores fora do Azure
> - Service Fabric
> - Application Insights
> - Contentores
> - Registos personalizados criados pela API do recoletor de dados de HTTP
>
> Pode testar se os registos estão corretamente associados com seus recursos ao executar uma consulta e inspecionar os registos que está interessado. Se o ID de recurso correto está a [_ResourceId](log-standard-properties.md#_resourceid) propriedade, em seguida, dados está disponível para consultas centrada no recurso.

### <a name="comparing-access-modes"></a>Comparar os modos de acesso

A tabela seguinte resume os modos de acesso:

| | Voltada para a área de trabalho | Centrado em recursos |
|:---|:---|:---|
| Quem é se destina a cada modelo? | Administração central. Administradores que precisam de configurar a recolha de dados e os utilizadores que necessitam de aceder a uma grande variedade de recursos. Também necessário atualmente para utilizadores que têm para aceder aos registos para recursos fora do Azure. | Equipes de aplicação. Administradores de recursos do Azure a ser monitorizados. |
| O que requer um utilizador para ver os registos? | Permissões para a área de trabalho. Ver **permissões de área de trabalho** na [gerir contas e utilizadores](#manage-accounts-and-users). | Acesso de leitura para o recurso. Ver **permissões de recursos** na [gerir contas e utilizadores](#manage-accounts-and-users). As permissões podem ser herdadas (por exemplo, o grupo de recursos que contêm) ou diretamente atribuído ao recurso. Permissão para os registos para o recurso será automaticamente atribuído. |
| O que é o âmbito de permissões? | Área de trabalho. Os utilizadores com acesso à área de trabalho podem consultar todos os registos nessa área de trabalho de tabelas que têm permissões para. Consulte [controlo de acesso de tabela](#table-access-control) | Recursos do Azure. Utilizador pode consultar os registos para recursos tem acesso a partir de qualquer área de trabalho, mas não é possível consultar os registos para outros recursos. |
| Como pode aceder aos registos de utilizador? | Inicie **registos** partir **Azure Monitor** menu ou **áreas de trabalho do Log Analytics**. | Inicie **registos** no menu para o recurso do Azure. |


## <a name="access-control-mode"></a>Modo de controlo de acesso
O _modo de controlo de acesso_ é uma configuração em cada áreas de trabalho que define como as permissões são determinadas para essa área de trabalho.

**Necessitam de permissões de área de trabalho**:  Este modo de controlo não permite granular RBAC. Para um utilizador aceder a área de trabalho, eles tem de possuir permissões para a área de trabalho ou tabelas específicas. 

Se um usuário acessa a área de trabalho no modo voltada para a área de trabalho, terão acesso a todos os dados todas as tabelas que tiver sido concedidos acesso. Se um usuário acessa a área de trabalho no modo centrada em recursos, terá acesso aos dados apenas para esse recurso em todas as tabelas que tiver sido concedidos acesso a.

Esta é a predefinição para todas as áreas de trabalho criadas antes de Março de 2019.

**Utilizar permissões de recursos ou a área de trabalho**: Este modo de controle permite que o granular RBAC. Os utilizadores que ser terão acesso a apenas os dados associados a recursos que podem visualizar por meio de permissões do Azure, recursos para os quais têm `read` permissão. 

Quando um usuário acessa a área de trabalho no modo voltada para a área de trabalho, permissões de área de trabalho serão aplicada. Quando um usuário acessa a área de trabalho no modo centrada em recursos, apenas as permissões de recursos serão verificadas e permissões de área de trabalho serão ignoradas. Ative o RBAC para um utilizador removê-los das permissões de área de trabalho e permitindo que suas permissões de recurso a ser reconhecida.

Esta é a predefinição para todas as áreas de trabalho criadas depois de Março de 2019.

> [!NOTE]
> Se um utilizador tem permissões de recursos apenas para a área de trabalho, apenas poderão aceder a área de trabalho utilizar [voltada para o recurso modo](#access-modes).


### <a name="define-access-control-mode-in-azure-portal"></a>Definir o modo de controlo de acesso no portal do Azure
Pode ver o modo de controlo de acesso à área de trabalho atual na **descrição geral** página para a área de trabalho a **área de trabalho do Log Analytics** menu.

![Modo de controlo de acesso de área de trabalho de modo de exibição](media/manage-access/view-access-control-mode.png)

Pode alterar esta definição no **propriedades** página para a área de trabalho. Alterar a definição será desativada se não tiver permissões para configurar a área de trabalho.

![Modo de acesso de área de trabalho de alteração](media/manage-access/change-access-control-mode.png)

### <a name="define-access-control-mode-in-azure-portal"></a>Definir o modo de controlo de acesso no portal do Azure
Pode ver o modo de controlo de acesso à área de trabalho atual na **descrição geral** página para a área de trabalho a **área de trabalho do Log Analytics** menu.

![Modo de controlo de acesso de área de trabalho de modo de exibição](media/manage-access/view-access-control-mode.png)

Pode alterar esta definição no **propriedades** página para a área de trabalho. Alterar a definição será desativada se não tiver permissões para configurar a área de trabalho.

![Modo de acesso de área de trabalho de alteração](media/manage-access/change-access-control-mode.png)

### <a name="define-access-control-mode-in-powershell"></a>Definir o modo de controlo de acesso no PowerShell

Utilize o seguinte comando para examinar o modo de controlo de acesso para todas as áreas de trabalho na subscrição:

```PowerShell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

Utilize o seguinte script para definir o modo de controlo de acesso para uma área de trabalho específico:

```PowerShell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Utilize o seguinte script para definir o modo de controlo de acesso para todas as áreas de trabalho na subscrição

```PowerShell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="define-access-mode-in-resource-manager-template"></a>Definir o modo de acesso no modelo do Resource Manager
Para configurar o modo de acesso num modelo Azure Resource Manager, defina o **enableLogAccessUsingOnlyResourcePermissions** sinalizador na área de trabalho para um dos seguintes valores de recursos.

- **FALSO**: Defina a área de trabalho às permissões voltada para a área de trabalho. Esta é a predefinição se o sinalizador não está definido.
- **Verdadeiro**: Defina a área de trabalho às permissões centrada no recurso.


## <a name="manage-accounts-and-users"></a>Gerir contas e utilizadores
As permissões para a área de trabalho que são aplicadas a um utilizador específico são definidas pelo seu modo de acesso e o [modo de controlo de acesso](#access-control-mode) da área de trabalho. **Permissões de área de trabalho** são aplicadas quando um utilizador acede a qualquer área de trabalho utilizar **voltada para a área de trabalho** no [modo voltada para a área de trabalho](#access-modes). **Permissões de recursos** são aplicadas quando um utilizador acede a uma área de trabalho com **utilizar permissões de recursos ou a área de trabalho** [modo de controlo de acesso](#access-control-mode) usando [modo centrada no recurso ](#access-modes).

### <a name="workspace-permissions"></a>Permissões de área de trabalho
Cada área de trabalho pode ter múltiplas contas associadas e cada conta pode ter acesso a várias áreas de trabalho. O acesso é gerido através de [acesso baseado em função do Azure](../../role-based-access-control/role-assignments-portal.md). 


As atividades seguintes também necessitam de permissões do Azure:

| Ação                                                          | Permissões do Azure Necessárias | Notas |
|-----------------------------------------------------------------|--------------------------|-------|
| Adicionar e remover soluções de monitorização                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Estas permissões têm de ser concedidas ao nível do grupo de recursos ou da subscrição. |
| Alterar o escalão de preço                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Ver dados nos mosaicos de solução *Backup* e *Site Recovery* | Administrador/Coadministrador | Acede aos recursos implementados com o modelo de implementação clássica |
| Criar um espaço de trabalho no portal do Azure                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


#### <a name="manage-access-to-log-analytics-workspace-using-azure-permissions"></a>Gerir o acesso ao Log Analytics área de trabalho com permissões do Azure 
Para conceder acesso à área de trabalho do Log Analytics com permissões do Azure, siga os passos em [Utilize atribuições de funções para gerir o acesso aos recursos de subscrição do Azure](../../role-based-access-control/role-assignments-portal.md).

O Azure tem duas funções de utilizador incorporadas para áreas de trabalho do Log Analytics:
- Leitor do Log Analytics
- Contribuidor do Log Analytics

Os membros da função *Leitor do Log Analytics* podem:
- Ver e procurar todos os dados de monitorização 
- Ver e monitorizar as definições, incluindo ver a configuração dos diagnósticos do Azure em todos os recursos do Azure.

A função de leitor do Log Analytics inclui as seguintes ações do Azure:

| Tipo    | Permissão | Descrição |
| ------- | ---------- | ----------- |
| Ação | `*/read`   | Capacidade de ver todos os recursos do Azure e a configuração do recurso. Inclui ver: <br> o estado da extensão da máquina virtual <br> a configuração dos diagnósticos do Azure nos recursos <br> todas as propriedades e definições de todos os recursos |
| Ação | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Capacidade para executar consultas Log Search v2 |
| Ação | `Microsoft.OperationalInsights/workspaces/search/action` | Capacidade para executar consultas Log Search v1 |
| Ação | `Microsoft.Support/*` | Capacidade para abrir pedidos de suporte |
|Ação Não | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Impede a leitura da área de trabalho chave necessária para utilizar a API de recolha de dados e para instalar agentes. Isso evita que o utilizador a adição de novos recursos para a área de trabalho |


Os membros da função *Contribuidor do Log Analytics* podem:
- Ler todos os dados de monitorização, como o leitor do Log Analytics pode  
- Criar e configurar contas de Automatização  
- Adicionar e remover soluções de gestão    
    > [!NOTE] 
    > Para realizar com êxito as últimas duas ações, esta permissão tem de ser concedidas ao nível de grupo ou uma subscrição do recurso.  

- Ler as chaves das contas de armazenamento   
- Configurar a recolha de registos a partir do Armazenamento do Azure  
- Editar as definições de monitorização para recursos do Azure, incluindo
  - Adicionar a extensão de VM a VMs
  - Configurar os diagnósticos do Azure em todos os recursos do Azure

> [!NOTE] 
> Pode utilizar a capacidade de adicionar uma extensão de máquina virtual a máquinas virtuais para obter o controlo total das mesmas.

A função de Contribuidor do Log Analytics inclui as seguintes ações do Azure:

| Permissão | Descrição |
| ---------- | ----------- |
| `*/read`     | Capacidade para ver todos os recursos e configuração dos mesmos. Inclui ver: <br> o estado da extensão da máquina virtual <br> a configuração dos diagnósticos do Azure nos recursos <br> todas as propriedades e definições de todos os recursos |
| `Microsoft.Automation/automationAccounts/*` | Capacidade para criar, e configurar contas de Automatização do Azure, incluindo adicionar e editar runbooks |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Adicionar, atualizar e remover extensões de máquinas virtuais, incluindo a extensão Microsoft Monitoring Agent e o Agente do OMS para a extensão do Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Ver a chave da conta de armazenamento. Necessária para configurar o Log Analytics para ler registos a partir das contas de Armazenamento do Azure |
| `Microsoft.Insights/alertRules/*` | Adicionar, atualizar e remover regras de alertas |
| `Microsoft.Insights/diagnosticSettings/*` | Adicionar, atualizar e remover as definições de diagnósticos em recursos do Azure |
| `Microsoft.OperationalInsights/*` | Adicionar, atualizar e remover a configuração de áreas de trabalho do Log Analytics |
| `Microsoft.OperationsManagement/*` | Adicionar e remover soluções de gestão |
| `Microsoft.Resources/deployments/*` | Criar e eliminar as implementações. Necessário para adicionar e remover soluções, áreas de trabalho e contas de automatização |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Criar e eliminar as implementações. Necessário para adicionar e remover soluções, áreas de trabalho e contas de automatização |

Para adicionar e remover utilizadores de uma função de utilizador, é necessário ter as permissões `Microsoft.Authorization/*/Delete` e `Microsoft.Authorization/*/Write`.

Utilize estas funções para conceder aos utilizadores acesso em âmbitos diferentes:
- Subscrição - acesso a todas as áreas de trabalho da subscrição
- Grupo de Recursos - acesso a todas as áreas de trabalho no grupo de recursos
- Recurso - acesso apenas à área de trabalho especificada

Deve realizar atribuições ao nível do recurso (área de trabalho), para garantir o controlo de acesso precisos.  Utilize [funções personalizadas](../../role-based-access-control/custom-roles.md) para criar funções com as permissões específicas necessárias.

### <a name="resource-permissions"></a>Permissões de recursos 
Quando a consulta de utilizadores inicia a partir de uma área de trabalho através do acesso centrada no recurso, terão as seguintes permissões no recurso:

| Permissão | Descrição |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Exemplos:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Capacidade de ver todos os dados de registo para o recurso.  |


Esta permissão é concedida normalmente de uma função que inclua  _\*/leitura ou_ _\*_ permissões como a conta interna [leitor](../../role-based-access-control/built-in-roles.md#reader) e [ Contribuinte](../../role-based-access-control/built-in-roles.md#contributor) funções. Tenha em atenção que as funções personalizadas que incluem ações específicas ou funções incorporadas dedicadas poderão não incluir esta permissão.

Ver [definir o controlo de acesso por tabela](#defining-per-table-access-control) abaixo se pretende criar o controlo de acesso diferentes para diferentes tabelas.


## <a name="table-level-rbac"></a>Nível de tabela RBAC
**RBAC de nível de tabela** permite-lhe fornecer um controle mais granular aos dados numa área de trabalho do Log Analytics, além de outras permissões. Esse controle permite-lhe definir os tipos de dados específicos que podem ser acedidos apenas por um conjunto específico de utilizadores.

Implementar o controlo de acesso de tabela com [funções personalizadas do Azure](../../role-based-access-control/custom-roles.md) conceder ou negar o acesso específico [tabelas](../log-query/log-query-overview.md#how-azure-monitor-log-data-is-organized) na área de trabalho. Estas funções são aplicadas a áreas de trabalho com voltada para a área de trabalho ou recurso centrado [modos de controlo de acesso](#access-control-modes) independentemente do utilizador [modo de acesso](#access-mode).

Criar uma [função personalizada](../../role-based-access-control/custom-roles.md) com as seguintes ações para definir o acesso ao controlo de acesso de tabela.

- Para conceder acesso a uma tabela, incluí-lo no **ações** secção da definição de função.
- Para negar o acesso a uma tabela, incluí-lo no **NotActions** secção da definição de função.
- Utilize * para especificar todas as tabelas.

Por exemplo, para criar uma função de acesso para o _Heartbeat_ e _AzureActivity_ tabelas, criar uma função personalizada com as seguintes ações:

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Para criar uma função com o acesso apenas _SecurityBaseline_ e nenhuma outra tabela, crie uma função personalizada com as seguintes ações:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
```

### <a name="custom-logs"></a>Registos personalizados
 Registos personalizados são criados por fontes de dados, tais como registos personalizados e a HTTP Data Collector API. A maneira mais fácil de identificar o tipo de registo está verificando as tabelas listadas na [Custom Logs no esquema do registo](../log-query/get-started-portal.md#understand-the-schema).

 Atualmente não é possível conceder ou negar o acesso a registos personalizados individuais, mas pode conceder ou negar o acesso a todos os registos personalizados. Para criar uma função de acesso a todos os registos personalizados, crie uma função personalizada com as seguintes ações:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>Considerações

- Se um utilizador é concedido global ler permissão com as funções de leitor ou contribuinte padrão que incluem o  _\*/leitura_ ação, ele irá substituir o controlo de acesso por tabela e dão-lhes acesso a todos os dados de registo.
- Se um utilizador é concedido acesso por tabela, mas sem outras permissões, eles conseguirão aceder aos dados de registo da API, mas não a partir do portal do Azure. Para fornecer acesso ao portal do Azure, utilize o leitor do Log Analytics como sua função de base.
- Administradores da subscrição têm acesso a todos os tipos de dados, independentemente de quaisquer outras definições de permissão.
- Os proprietários de área de trabalho são tratados como qualquer outro utilizador para o controlo de acesso-table.
- Deve atribuir funções para grupos de segurança em vez de utilizadores individuais para reduzir o número de atribuições. Isso também o ajudará a utilizar as ferramentas de gestão de grupo existentes para configurar e verificar o acesso.




## <a name="next-steps"></a>Passos Seguintes
* Ver [descrição geral do agente do Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) para recolher dados de computadores no seu datacenter ou outro ambiente de cloud.
* Consulte [Recolher dados sobre Máquinas Virtuais do Azure](../../azure-monitor/learn/quick-collect-azurevm.md) para configurar a recolha de dados a partir de VMs do Azure.  

