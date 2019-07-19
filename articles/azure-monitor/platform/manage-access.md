---
title: Gerenciar espaços de trabalho do Log Analytics no Azure Monitor | Microsoft Docs
description: Você pode gerenciar Log Analytics espaços de trabalho no Azure Monitor usando uma variedade de tarefas administrativas em usuários, contas, espaços de trabalho e contas do Azure.
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
ms.date: 07/16/2019
ms.author: magoedte
ms.openlocfilehash: fbfbd8e26ab3e92f06194322be7ec2be2fb180fd
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68254452"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>Gerenciar dados de log e espaços de trabalho no Azure Monitor
O Azure Monitor armazena dados de log em um espaço de trabalho Log Analytics, que é essencialmente um contêiner que inclui dados e informações de configuração. Para gerenciar o acesso aos dados de log, você executa várias tarefas administrativas relacionadas aos espaços de trabalho. O utilizador ou outros membros da sua organização podem utilizar várias áreas de trabalho para gerir diferentes conjuntos de dados recolhidos da totalidade ou de partes da sua infraestrutura de TI.

Este artigo explica como gerenciar o acesso aos logs e administrar os espaços de trabalho que os contêm. 

## <a name="create-a-workspace"></a>Criar uma área de trabalho
Para criar um espaço de trabalho Log Analytics, você precisa:

1. Ter uma subscrição do Azure.
2. Escolher um nome de área de trabalho.
3. Associe a área de trabalho uma das suas subscrições e grupos de recursos.
4. Escolher uma localização geográfica.

Consulte os seguintes artigos para obter detalhes sobre como criar um espaço de trabalho:

- [Criar um espaço de trabalho Log Analytics no portal do Azure](../learn/quick-create-workspace.md)
- [Criar um espaço de trabalho Log Analytics com o CLI do Azure 2,0](../learn/quick-create-workspace-cli.md)
- [Criar um espaço de trabalho Log Analytics com Azure PowerShell](../learn/quick-create-workspace-posh.md)

## <a name="determine-the-number-of-workspaces-you-need"></a>Determinar o número de áreas de trabalho necessárias
Um espaço de trabalho Log Analytics é um recurso do Azure e é um contêiner em que os dados são coletados, agregados, analisados e apresentados em Azure Monitor. Você pode ter vários espaços de trabalho por assinatura do Azure e pode ter acesso a mais de um espaço de trabalho, com a capacidade de consultar facilmente entre eles. Esta secção descreve quando pode ser útil criar mais do que uma área de trabalho.

Um espaço de trabalho Log Analytics fornece:

* Uma localização geográfica para armazenamento de dados.
* Isolamento de dados para definir direitos de acesso de usuário diferentes no modo centrado no espaço de trabalho. Não é relevante ao trabalhar no modo centrado em recursos.
* Escopo para configuração de configurações, como [tipo de preço](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [retenção](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) e limitação de [dados](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap).
* Os encargos relacionados à ingestão de dados e retenção são feitos no recurso de espaço de trabalho.

Do ponto de vista para consumo, é recomendável que criar áreas de trabalho mínimo possível. Faz administração e a experiência de consulta mais fácil e rápido. Mas, com base nas características anteriores, talvez queira criar várias áreas de trabalho se:

* Você é uma empresa global e precisa de dados de log armazenados em regiões específicas para fins de conformidade ou de soberania de dados.
* Estiver a utilizar o Azure e pretender evitar custos de transferência de dados de saída ao ter uma área de trabalho na mesma região dos recursos do Azure que gere.
* É um fornecedor de serviços geridos e necessita de manter os dados do Log Analytics de cada cliente gerido isolado em relação aos de outros clientes.
* Você gerencia vários clientes e deseja que cada cliente/departamento/grupo de negócios Veja seus próprios dados, mas não dados de outros, e não há necessidade comercial de uma exibição consolidada entre clientes/departamentos/grupos de negócios. ".

Quando utilizar agentes do Windows para recolher dados, pode [configurar cada agente para comunicar a uma ou mais áreas de trabalho](../../azure-monitor/platform/agent-windows.md).

Se estiver a utilizar o System Center Operations Manager, cada grupo de gestão do Operations Manager só pode ser ligado a uma área de trabalho. Pode instalar o Microsoft Monitoring Agent em computadores geridos pelo Operations Manager e fazer com que o agente reporte ao Operations Manager e a uma área de trabalho do Log Analytics diferente.

Depois que a arquitetura do espaço de trabalho for definida, você deverá impor essa política nos recursos do Azure com [Azure Policy](../../governance/policy/overview.md). Isso pode fornecer uma definição interna que se aplicará automaticamente a todos os recursos do Azure. Por exemplo, você pode definir uma política para garantir que todos os seus recursos do Azure em uma região específica enviaram todos os seus logs de diagnóstico para um espaço de trabalho específico.

## <a name="view-workspace-details"></a>Exibir detalhes do espaço de trabalho
Ao analisar dados em seu espaço de trabalho do Log Analytics no menu **Azure monitor** na portal do Azure, você cria e gerencia espaços de trabalho no menu **log Analytics espaços de trabalho** .
 

1. Entre no [portal do Azure](https://portal.azure.com) e clique em **todos os serviços**. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **log Analytics** espaços de trabalho.  

    ![Portal do Azure](media/manage-access/azure-portal-01.png)  

3. Selecione seu espaço de trabalho na lista.

4. A página espaço de trabalho exibe detalhes sobre o espaço de trabalho, introdução, configuração e links para obter informações adicionais.  

    ![Detalhes da área de trabalho](./media/manage-access/workspace-overview-page.png)  


## <a name="workspace-permissions-and-scope"></a>Permissões e escopo do espaço de trabalho
Os dados aos quais um usuário tem acesso são determinados por vários fatores listados na tabela a seguir. Cada um é descrito nas seções a seguir.

| Fator | Descrição |
|:---|:---|
| [Modo de acesso](#access-modes) | Método que o usuário usa para acessar o espaço de trabalho.  Define o escopo dos dados disponíveis e o modo de controle de acesso que é aplicado. |
| [Modo de controle de acesso](#access-control-mode) | Configuração no espaço de trabalho que define se as permissões são aplicadas no nível de espaço de trabalho ou de recurso. |
| [Permissões](#manage-accounts-and-users) | Permissões aplicadas a indivíduo ou grupos de usuários para o espaço de trabalho ou recurso. Define a quais dados o usuário terá acesso. |
| [RBAC de nível de tabela](#table-level-rbac) | Permissões granulares opcionais que se aplicam a todos os usuários, independentemente do modo de acesso ou do modo de controle de acesso. Define quais tipos de dados um usuário pode acessar. |



## <a name="access-modes"></a>Modos de acesso
O _modo de acesso_ refere-se a como um usuário acessa um espaço de trabalho log Analytics e define o escopo dos dados que eles podem acessar. 

**Centrado no espaço de trabalho**: Nesse modo, um usuário pode exibir todos os logs no espaço de trabalho para os quais eles têm permissões. As consultas nesse modo têm o escopo definido para todos os dados em todas as tabelas no espaço de trabalho. Esse é o modo de acesso usado quando os logs são acessados com o espaço de trabalho como o escopo, como quando você seleciona **logs** no menu **Azure monitor** na portal do Azure.

**Centrado em recursos**: Ao acessar o espaço de trabalho para um recurso específico, como quando você seleciona **logs** em um menu de recursos na portal do Azure, você pode exibir logs somente para esse recurso em todas as tabelas às quais você tem acesso. As consultas neste modo têm o escopo para apenas os dados associados a esse recurso. Esse modo também habilita o RBAC (controle de acesso baseado em função) granular. 

> [!NOTE]
> Os logs estarão disponíveis para consultas centradas em recursos somente se estiverem corretamente associados ao recurso relevante. Atualmente, os seguintes recursos têm limitações: 
> - Computadores fora do Azure
> - Service Fabric
> - Application Insights
> - Contentores
>
> Você pode testar se os logs estão corretamente associados ao recurso executando uma consulta e inspecionando os registros nos quais você está interessado. Se a ID de recurso correta estiver na propriedade [_ResourceId](log-standard-properties.md#_resourceid) , os dados estarão disponíveis para consultas centradas em recursos.

### <a name="comparing-access-modes"></a>Comparando modos de acesso

A tabela a seguir resume os modos de acesso:

| | Centrado no espaço de trabalho | Centrado em recursos |
|:---|:---|:---|
| A quem cada modelo se destina? | Administração Central. Os administradores que precisam configurar a coleta de dados e os usuários que precisam de acesso a uma ampla variedade de recursos. Também obrigatório no momento para usuários que precisam acessar logs para recursos fora do Azure. | Equipes de aplicativos. Administradores de recursos do Azure sendo monitorados. |
| O que um usuário precisa para exibir os logs? | Permissões para o espaço de trabalho. Consulte **permissões de espaço de trabalho** em [gerenciar contas e usuários](#manage-accounts-and-users). | Acesso de leitura ao recurso. Consulte **permissões de recurso** em [gerenciar contas e usuários](#manage-accounts-and-users). As permissões podem ser herdadas (por exemplo, do grupo de recursos que a contém) ou diretamente atribuídas ao recurso. A permissão para os logs do recurso será atribuída automaticamente. |
| Qual é o escopo das permissões? | Espaço. Os usuários com acesso ao espaço de trabalho podem consultar todos os logs nesse espaço de trabalho de tabelas às quais eles têm permissões. Consulte [controle de acesso à tabela](#table-level-rbac) | Recurso do Azure. O usuário pode consultar os logs de recursos aos quais eles têm acesso de qualquer espaço de trabalho, mas não pode consultar logs para outros recursos. |
| Como o usuário pode acessar os logs? | Inicie **logs** no menu **Azure monitor** ou em **espaços de trabalho do log Analytics**. | Inicie **os logs** no menu do recurso do Azure. |


## <a name="access-control-mode"></a>Modo de controle de acesso
O _modo de controle de acesso_ é uma configuração em cada espaço de trabalho que define como as permissões são determinadas para esse espaço de trabalho.

**Exigir permissões de espaço de trabalho**:  Esse modo de controle não permite RBAC granular. Para que um usuário acesse o espaço de trabalho, ele deve receber permissões para o espaço de trabalho ou para tabelas específicas. 

Se um usuário acessar o espaço de trabalho no modo centrado no espaço de trabalho, ele terá acesso a todos os dados quaisquer tabelas às quais tenha sido concedido acesso. Se um usuário acessar o espaço de trabalho no modo centrado em recursos, ele terá acesso a apenas dados para esse recurso em qualquer tabela à qual tenha sido concedido acesso.

Essa é a configuração padrão para todos os espaços de trabalho criados antes de março de 2019.

**Usar permissões de recurso ou espaço de trabalho**: Esse modo de controle permite RBAC granular. Os usuários recebem acesso a apenas dados associados a recursos que podem ser exibidos por meio de permissões do Azure, recursos para `read` os quais eles têm permissão. 

Quando um usuário acessa o espaço de trabalho no modo centrado no espaço de trabalho, as permissões de espaço de trabalho serão aplicadas. Quando um usuário acessa o espaço de trabalho no modo centrado em recursos, somente as permissões de recurso serão verificadas e as permissões do espaço de trabalho serão ignoradas. Habilite o RBAC para um usuário removendo-os das permissões do espaço de trabalho e permitindo que suas permissões de recurso sejam reconhecidas.

Essa é a configuração padrão para todos os espaços de trabalho criados depois de março de 2019.

> [!NOTE]
> Se um usuário tiver apenas permissões de recurso para o espaço de trabalho, ele só poderá acessar o espaço de trabalho usando o [modo centrado em recursos](#access-modes).


### <a name="define-access-control-mode-in-azure-portal"></a>Definir o modo de controle de acesso no portal do Azure
Você pode exibir o modo de controle de acesso do espaço de trabalho atual na página **visão geral** do espaço de trabalho no menu **log Analytics espaço de trabalho** .

![Exibir modo de controle de acesso do espaço de trabalho](media/manage-access/view-access-control-mode.png)

Você pode alterar essa configuração na página **Propriedades** do espaço de trabalho. A alteração da configuração será desabilitada se você não tiver permissões para configurar o espaço de trabalho.

![Alterar modo de acesso do espaço de trabalho](media/manage-access/change-access-control-mode.png)

### <a name="define-access-control-mode-in-powershell"></a>Definir o modo de controle de acesso no PowerShell

Use o seguinte comando para examinar o modo de controle de acesso para todos os espaços de trabalho na assinatura:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

Use o script a seguir para definir o modo de controle de acesso para um espaço de trabalho específico:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Use o script a seguir para definir o modo de controle de acesso para todos os espaços de trabalho na assinatura

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="define-access-mode-in-resource-manager-template"></a>Definir o modo de acesso no modelo do Resource Manager
Para configurar o modo de acesso em um modelo de Azure Resource Manager, defina o sinalizador de recurso **enableLogAccessUsingOnlyResourcePermissions** no espaço de trabalho para um dos valores a seguir.

- **falso**: Defina o espaço de trabalho como permissões centradas no espaço de trabalho. Essa será a configuração padrão se o sinalizador não estiver definido.
- **verdadeiro**: Defina o espaço de trabalho para permissões centradas em recursos.


## <a name="manage-accounts-and-users"></a>Gerir contas e utilizadores
As permissões para o espaço de trabalho que são aplicadas a um determinado usuário são definidas por seu modo de acesso e o [modo de controle de acesso](#access-control-mode) do espaço de trabalho. **As permissões de espaço de trabalho** são aplicadas quando um usuário acessa qualquer espaço de trabalho usando o **espaço** de trabalho centrado no [modo centrado no espaço de trabalho](#access-modes). **As permissões de recurso** são aplicadas quando um usuário acessa um espaço de trabalho com o [modo de controle de acesso](#access-control-mode) de permissões de espaço de **trabalho ou recurso** usando o [modo centrado em recursos](#access-modes).

### <a name="workspace-permissions"></a>Permissões do espaço de trabalho
Cada área de trabalho pode ter múltiplas contas associadas e cada conta pode ter acesso a várias áreas de trabalho. O acesso é gerido através de [acesso baseado em função do Azure](../../role-based-access-control/role-assignments-portal.md). 


As atividades seguintes também necessitam de permissões do Azure:

||Ação |Permissões do Azure Necessárias |Notas |
|-------|-------------------------|------|
| Adicionando e removendo soluções de monitoramento | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Estas permissões têm de ser concedidas ao nível do grupo de recursos ou da subscrição. |
| Alterar o escalão de preço | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Ver dados nos mosaicos de solução *Backup* e *Site Recovery* | Administrador/Coadministrador | Acede aos recursos implementados com o modelo de implementação clássica |
| Criar um espaço de trabalho no portal do Azure | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| Exibir propriedades básicas do espaço de trabalho e inserir a folha do espaço de trabalho no portal | `Microsoft.OperationalInsights/workspaces/read` ||
| Consultar logs usando qualquer interface | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Acessar todos os tipos de log usando consultas | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Acessar uma tabela de log específica | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| Ler as chaves do espaço de trabalho para permitir o envio de logs para este espaço de trabalho | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||



#### <a name="manage-access-to-log-analytics-workspace-using-azure-permissions"></a>Gerenciar o acesso ao Log Analytics espaço de trabalho usando permissões do Azure 
Para conceder acesso à área de trabalho do Log Analytics com permissões do Azure, siga os passos em [Utilize atribuições de funções para gerir o acesso aos recursos de subscrição do Azure](../../role-based-access-control/role-assignments-portal.md).

O Azure tem duas funções de usuário internas para Log Analytics espaços de trabalho:
- Leitor do Log Analytics
- Contribuidor do Log Analytics

Os membros da função *Leitor do Log Analytics* podem:
- Ver e procurar todos os dados de monitorização 
- Ver e monitorizar as definições, incluindo ver a configuração dos diagnósticos do Azure em todos os recursos do Azure.

A função de leitor do Log Analytics inclui as seguintes ações do Azure:

| Tipo    | Permissão | Descrição |
| ------- | ---------- | ----------- |
| Ação | `*/read`   | Capacidade de ver todos os recursos do Azure e a configuração do recurso. Inclui ver: <br> o estado da extensão da máquina virtual <br> a configuração dos diagnósticos do Azure nos recursos <br> Todas as propriedades e configurações de todos os recursos. <br> Para espaços de trabalho, ele permite permissões totalmente irrestritas para ler as configurações do espaço de trabalho e executar a consulta nos dados. Veja as opções mais granulares acima. |
| Action | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Preterido, sem necessidade de atribuí-los a usuários. |
| Action | `Microsoft.OperationalInsights/workspaces/search/action` | Preterido, sem necessidade de atribuí-los aos usuários. |
| Action | `Microsoft.Support/*` | Capacidade para abrir pedidos de suporte |
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
| `*/read`     | Capacidade de ver todos os recursos do Azure e a configuração do recurso. Inclui ver: <br> o estado da extensão da máquina virtual <br> a configuração dos diagnósticos do Azure nos recursos <br> Todas as propriedades e configurações de todos os recursos. <br> Para espaços de trabalho, ele permite permissões totalmente irrestritas para ler as configurações do espaço de trabalho e executar a consulta nos dados. Veja as opções mais granulares acima. |
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

Você deve executar atribuições no nível de recurso (espaço de trabalho) para garantir o controle de acesso preciso.  Utilize [funções personalizadas](../../role-based-access-control/custom-roles.md) para criar funções com as permissões específicas necessárias.

### <a name="resource-permissions"></a>Permissões de recurso 
Quando os usuários consultam logs de um espaço de trabalho usando acesso centrado em recursos, eles terão as seguintes permissões no recurso:

| Permissão | Descrição |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Exemplos:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Capacidade de exibir todos os dados de log para o recurso.  |
| `Microsoft.Insights/diagnosticSettings/write ` | Capacidade de definir a configuração de diagnóstico para permitir a configuração de logs para este recurso. |

Essa permissão geralmente é concedida de uma função que inclui  _\*/Read ou_ _\*_ permissões, como as funções de [leitor](../../role-based-access-control/built-in-roles.md#reader) e [colaborador](../../role-based-access-control/built-in-roles.md#contributor) internas. Observe que as funções personalizadas que incluem ações específicas ou funções internas dedicadas podem não incluir essa permissão.

Consulte [definindo o controle de acesso por tabela](#table-level-rbac) abaixo se você quiser criar um controle de acesso diferente para tabelas diferentes.


## <a name="table-level-rbac"></a>RBAC de nível de tabela
O **RBAC em nível de tabela** permite que você forneça controle mais granular aos dados em um espaço de trabalho log Analytics além das outras permissões. Esse controle permite que você defina tipos de dados específicos que são acessíveis somente para um conjunto específico de usuários.

Você implementa o controle de acesso de tabela com as [funções personalizadas do Azure](../../role-based-access-control/custom-roles.md) para conceder ou negar acesso a [tabelas](../log-query/logs-structure.md) específicas no espaço de trabalho. Essas funções são aplicadas a espaços de trabalho com [modos de controle de acesso](#access-control-mode) centrados no espaço de trabalho ou centralizado em recursos, independentemente do modo de [acesso](#access-modes)do usuário.

Crie uma [função personalizada](../../role-based-access-control/custom-roles.md) com as ações a seguir para definir o acesso ao controle de acesso à tabela.

- Para conceder acesso a uma tabela, inclua-a na seção **ações** da definição de função.
- Para negar o acesso a uma tabela, inclua-a na seção não- **ações** da definição de função.
- Use * para especificar todas as tabelas.

Por exemplo, para criar uma função com acesso às tabelas _Heartbeat_ e _AzureActivity_ , crie uma função personalizada usando as seguintes ações:

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Para criar uma função com acesso somente a _SecurityBaseline_ e nenhuma outra tabela, crie uma função personalizada usando as seguintes ações:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
```

### <a name="custom-logs"></a>Registos personalizados
 Os logs personalizados são criados por fontes de dados, como logs personalizados e API do coletor de dados HTTP. A maneira mais fácil de identificar o tipo de log é verificando as tabelas listadas em [logs personalizados no esquema de log](../log-query/get-started-portal.md#understand-the-schema).

 No momento, não é possível conceder ou negar acesso a logs personalizados individuais, mas você pode conceder ou negar acesso a todos os logs personalizados. Para criar uma função com acesso a todos os logs personalizados, crie uma função personalizada usando as seguintes ações:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>Considerações

- Se um usuário receber permissão de leitura global com as funções leitor padrão ou colaborador que incluem a  _\*ação/Read_ , ele substituirá o controle de acesso por tabela e dará acesso a todos os dados de log.
- Se um usuário receber acesso por tabela, mas nenhuma outra permissão, ele poderá acessar dados de log da API, mas não do portal do Azure. Para fornecer acesso ao portal do Azure, use o leitor de Log Analytics como sua função base.
- Os administradores da assinatura terão acesso a todos os tipos de dados, independentemente de quaisquer outras configurações de permissão.
- Os proprietários do espaço de trabalho são tratados como qualquer outro usuário para o controle de acesso por tabela.
- Você deve atribuir funções a grupos de segurança em vez de usuários individuais para reduzir o número de atribuições. Isso também ajudará você a usar as ferramentas de gerenciamento de grupo existentes para configurar e verificar o acesso.




## <a name="next-steps"></a>Passos Seguintes
* Ver [descrição geral do agente do Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) para recolher dados de computadores no seu datacenter ou outro ambiente de cloud.
* Consulte [Recolher dados sobre Máquinas Virtuais do Azure](../../azure-monitor/learn/quick-collect-azurevm.md) para configurar a recolha de dados a partir de VMs do Azure.  

