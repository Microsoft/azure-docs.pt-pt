---
title: Log de diagnóstico para Azure Analysis Services | Microsoft Docs
description: Saiba mais sobre como configurar o log de diagnóstico para Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a9684042a76c9c906a75334c319b4ca8ee0b727b
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298620"
---
# <a name="setup-diagnostic-logging"></a>Configurar registo de diagnósticos

Uma parte importante de qualquer solução de Analysis Services é monitorar o desempenho de seus servidores. Com [os logs de diagnóstico de recursos do Azure](../azure-monitor/platform/resource-logs-overview.md), você pode monitorar e enviar logs para o [armazenamento do Azure](https://azure.microsoft.com/services/storage/), transmiti-los para os [hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs/)e exportá-los para [logs de Azure monitor](../azure-monitor/azure-monitor-log-hub.md).

![Log de diagnóstico para armazenamento, hubs de eventos ou logs de Azure Monitor](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>O que está registrado em log?

Você pode selecionar as categorias **mecanismo**, **serviço**e **métricas** .

### <a name="engine"></a>Motores

Selecionar **mecanismo** registra todos os [xEvents](https://docs.microsoft.com/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Você não pode selecionar eventos individuais. 

|Categorias XEvent |Nome do evento  |
|---------|---------|
|Auditoria de Segurança    |   Logon de auditoria      |
|Auditoria de Segurança    |   Logout de auditoria      |
|Auditoria de Segurança    |   O servidor de auditoria é iniciado e interrompido      |
|Relatórios de andamento     |   Início do relatório de andamento      |
|Relatórios de andamento     |   Término do relatório de progresso      |
|Relatórios de andamento     |   Relatório de progresso atual      |
|Consultas     |  Início da consulta       |
|Consultas     |   Fim da consulta      |
|Comandos     |  Início do comando       |
|Comandos     |  Término do comando       |
|Erros & avisos     |   Erro      |
|Descobrir     |   Fim da descoberta      |
|Notificação     |    Notificação     |
|Sessão     |  Inicialização de sessão       |
|Bloqueios    |  Bloqueado       |
|Processamento de consulta     |   Início da consulta VertiPaq SE      |
|Processamento de consulta     |   Fim da consulta VertiPaq SE      |
|Processamento de consulta     |   Correspondência de cache de consulta VertiPaq SE      |
|Processamento de consulta     |   Início da consulta direta      |
|Processamento de consulta     |  Fim da consulta direta       |

### <a name="service"></a>Serviço

|Nome da operação  |Ocorre quando  |
|---------|---------|
|ResumeServer     |    Retomar um servidor     |
|SuspendServer    |   Pausar um servidor      |
|DeleteServer     |    Excluir um servidor     |
|RestartServer    |     O usuário reinicia um servidor por meio do SSMS ou do PowerShell    |
|GetServerLogFiles    |    O usuário exporta o log do servidor por meio do PowerShell     |
|ExportModel     |   O usuário exporta um modelo no portal usando abrir no Visual Studio     |

### <a name="all-metrics"></a>Todas as métricas

A categoria de métricas registra as mesmas [métricas de servidor](analysis-services-monitor.md#server-metrics) na tabela AzureMetrics. Se você estiver usando a [expansão](analysis-services-scale-out.md) de consulta e precisar separar métricas para cada réplica de leitura, use a tabela AzureDiagnostics em vez disso, em que **OperationName** é igual a **LogMetric**.

## <a name="setup-diagnostics-logging"></a>Configurar o registo de diagnósticos

### <a name="azure-portal"></a>Portal do Azure

1. Em [portal do Azure](https://portal.azure.com) > Server, clique em **logs de diagnóstico** no painel de navegação esquerdo e, em seguida, clique em **Ativar diagnóstico**.

    ![Ativar o log de diagnóstico para Azure Cosmos DB no portal do Azure](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. Em **configurações de diagnóstico**, especifique as seguintes opções: 

    * **Nome**. Insira um nome para os logs a serem criados.

    * **Arquivar em uma conta de armazenamento**. Para usar essa opção, você precisa de uma conta de armazenamento existente para se conectar. Consulte [criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md). Siga as instruções para criar um Gerenciador de recursos, uma conta de finalidade geral e, em seguida, selecione sua conta de armazenamento retornando a esta página no Portal. Pode levar alguns minutos para que contas de armazenamento criadas recentemente apareçam no menu suspenso.
    * **Transmitir para um hub de eventos**. Para usar essa opção, você precisa de um namespace de Hub de eventos e Hub de eventos existentes para se conectar. Para saber mais, confira [criar um namespace de hubs de eventos e um hub de eventos usando o portal do Azure](../event-hubs/event-hubs-create.md). Em seguida, retorne a esta página no portal para selecionar o nome do namespace e da política do hub de eventos.
    * **Enviar para Azure monitor (log Analytics espaço de trabalho)** . Para usar essa opção, use um espaço de trabalho existente ou [crie um novo](../azure-monitor/learn/quick-create-workspace.md) recurso de espaço de trabalho no Portal. Para obter mais informações sobre como exibir seus logs, consulte [Exibir logs no log Analytics espaço de trabalho](#view-logs-in-log-analytics-workspace) neste artigo.

    * **Mecanismo**. Selecione esta opção para registrar xEvents. Se você estiver arquivando em uma conta de armazenamento, poderá selecionar o período de retenção para os logs de diagnóstico. Os logs são excluídos em autotempo após o período de retenção expirar.
    * Do **serviço**. Selecione esta opção para registrar em log os eventos de nível de serviço. Se você estiver arquivando em uma conta de armazenamento, poderá selecionar o período de retenção para os logs de diagnóstico. Os logs são excluídos em autotempo após o período de retenção expirar.
    * **Métricas**. Selecione esta opção para armazenar dados detalhados em [métricas](analysis-services-monitor.md#server-metrics). Se você estiver arquivando em uma conta de armazenamento, poderá selecionar o período de retenção para os logs de diagnóstico. Os logs são excluídos em autotempo após o período de retenção expirar.

3. Clique em **Guardar**.

    Se você receber um erro que diz "falha ao atualizar o diagnóstico para o nome \<workspace >. A ID da assinatura \<subscription > não está registrada para usar o Microsoft. insights. " Siga as instruções de [diagnóstico do Azure de solução de problemas](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) para registrar a conta e repita esse procedimento.

    Se desejar alterar o modo como os logs de diagnóstico são salvos em qualquer ponto no futuro, você pode retornar a esta página para modificar as configurações.

### <a name="powershell"></a>PowerShell

Aqui estão os comandos básicos para você começar. Se você quiser obter ajuda passo a passo sobre como configurar o log para uma conta de armazenamento usando o PowerShell, consulte o tutorial mais adiante neste artigo.

Para habilitar as métricas e o log de diagnóstico usando o PowerShell, use os seguintes comandos:

- Para habilitar o armazenamento de logs de diagnóstico em uma conta de armazenamento, use este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   A ID da conta de armazenamento é a ID de recurso da conta de armazenamento para a qual você deseja enviar os logs.

- Para habilitar o streaming de logs de diagnóstico para um hub de eventos, use este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   A ID da regra do barramento de serviço do Azure é uma cadeia de caracteres com este formato:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Para habilitar o envio de logs de diagnóstico para um espaço de trabalho Log Analytics, use este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Você pode obter a ID de recurso do seu espaço de trabalho Log Analytics usando o seguinte comando:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Você pode combinar esses parâmetros para habilitar várias opções de saída.

### <a name="rest-api"></a>API REST

Saiba como [alterar as configurações de diagnóstico usando a API REST do Azure monitor](https://docs.microsoft.com/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Modelo do Resource Manager

Saiba como [habilitar as configurações de diagnóstico na criação de recursos usando um modelo do Resource Manager](../azure-monitor/platform/diagnostic-settings-template.md). 

## <a name="manage-your-logs"></a>Gerenciar seus logs

Os logs normalmente estão disponíveis em algumas horas de configuração de registro em log. Cabe-lhe gerir os seus registos na sua conta de armazenamento:

* Utilize métodos padrão de controlo de acesso do Azure para proteger os seus registos, restringindo o seu acesso.
* Elimine os registos que já não pretende manter na sua conta de armazenamento.
* Certifique-se de definir um período de retenção para que os logs antigos sejam excluídos da sua conta de armazenamento.

## <a name="view-logs-in-log-analytics-workspace"></a>Exibir logs no espaço de trabalho Log Analytics

As métricas e os eventos de servidor são integrados ao xEvents em seu recurso de espaço de trabalho Log Analytics para análise lado a lado. Log Analytics espaço de trabalho também pode ser configurado para receber eventos de outros serviços do Azure, fornecendo uma visão holística dos dados de log de diagnóstico em sua arquitetura.

Para exibir os dados de diagnóstico, em Log Analytics espaço de trabalho, abra **logs** no menu à esquerda.

![Opções de pesquisa de log no portal do Azure](./media/analysis-services-logging/aas-logging-open-log-search.png)

No construtor de consultas, expanda **LogManagement** > **AzureDiagnostics**. O AzureDiagnostics inclui eventos de mecanismo e serviço. Observe que uma consulta é criada imediatamente. O campo EventClass @ no__t-0s contém nomes de xEvent, que poderão parecer familiares se você tiver usado xEvents para registro em log local. Clique em **EventClass @ no__t-1s** ou em um dos nomes de evento e log Analytics espaço de trabalho continua construindo uma consulta. Certifique-se de salvar suas consultas para reutilizá-las mais tarde.

### <a name="example-queries"></a>Exemplos de consultas

#### <a name="example-1"></a>Exemplo 1

A consulta a seguir retorna durações para cada evento end/atualizar end da consulta para um banco de dados modelo e um servidor. Se for expandido, os resultados serão divididos por réplica porque o número da réplica está incluído em ServerName_s. O agrupamento por RootActivityId_g reduz a contagem de linhas recuperadas da API REST Diagnóstico do Azure e ajuda a permanecer dentro dos limites, conforme descrito em [limites de taxa de log Analytics](https://dev.loganalytics.io/documentation/Using-the-API/Limits).

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName" and DatabaseName_s =~ "MyDatabaseName" ;
window
| where OperationName has "QueryEnd" or (OperationName has "CommandEnd" and EventSubclass_s == 38)
| where extract(@"([^,]*)", 1,Duration_s, typeof(long)) > 0
| extend DurationMs=extract(@"([^,]*)", 1,Duration_s, typeof(long))
| project  StartTime_t,EndTime_t,ServerName_s,OperationName,RootActivityId_g,TextData_s,DatabaseName_s,ApplicationName_s,Duration_s,EffectiveUsername_s,User_s,EventSubclass_s,DurationMs
| order by StartTime_t asc
```

#### <a name="example-2"></a>Exemplo 2

A consulta a seguir retorna a memória e o consumo de QPU para um servidor. Se for expandido, os resultados serão divididos por réplica porque o número da réplica está incluído em ServerName_s.

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where name_s == "memory_metric" or name_s == "qpu_metric"
| project ServerName_s, TimeGenerated, name_s, value_s
| summarize avg(todecimal(value_s)) by ServerName_s, name_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

#### <a name="example-3"></a>Exemplo 3

A consulta a seguir retorna os contadores de desempenho de linhas lidas/s Analysis Services mecanismo para um servidor.

```Kusto
let window =  AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where parse_json(tostring(parse_json(perfobject_s).counters))[0].name == "Rows read/sec" 
| extend Value = tostring(parse_json(tostring(parse_json(perfobject_s).counters))[0].value) 
| project ServerName_s, TimeGenerated, Value
| summarize avg(todecimal(Value)) by ServerName_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

Há centenas de consultas que você pode usar. Para saber mais sobre consultas, consulte Introdução [às consultas de log de Azure monitor](../azure-monitor/log-query/get-started-queries.md).


## <a name="turn-on-logging-by-using-powershell"></a>Ativar o registro em log usando o PowerShell

Neste tutorial rápido, você cria uma conta de armazenamento na mesma assinatura e grupo de recursos que o servidor do Analysis Services. Em seguida, use set-AzDiagnosticSetting para ativar o log de diagnósticos, enviando a saída para a nova conta de armazenamento.

### <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você deve ter os seguintes recursos:

* Um servidor de Azure Analysis Services existente. Para obter instruções sobre como criar um recurso de servidor, consulte [criar um servidor em portal do Azure](analysis-services-create-server.md)ou [criar um servidor de Azure Analysis Services usando o PowerShell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Connect às suas assinaturas

Abra uma sessão no Azure PowerShell e inicie sessão na sua conta do Azure com o seguinte comando:  

```powershell
Connect-AzAccount
```

Na janela pop-up do browser, introduza o seu nome de utilizador da conta do Azure e a palavra-passe. O Azure PowerShell obtém todas as subscrições associadas a esta conta e, por defeito, utiliza a primeira.

Se tiver várias subscrições, poderá ter de especificar uma subscrição utilizada para criar o seu Cofre de Chaves do Azure. Escreva o seguinte para ver as subscrições da sua conta:

```powershell
Get-AzSubscription
```

Em seguida, para especificar a assinatura associada à conta de Azure Analysis Services que você está registrando, digite:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Se você tiver várias assinaturas associadas à sua conta, é importante especificar a assinatura.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Criar uma nova conta de armazenamento para os seus registos

Você pode usar uma conta de armazenamento existente para seus logs, desde que ela esteja na mesma assinatura que o servidor. Para este tutorial, você cria uma nova conta de armazenamento dedicada a logs de Analysis Services. Para facilitar, você está armazenando os detalhes da conta de armazenamento em uma variável chamada **SA**.

Você também usa o mesmo grupo de recursos que o que contém o servidor de Analysis Services. Substitua valores para `awsales_resgroup`, `awsaleslogs` e `West Central US` pelos seus próprios valores:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identificar a conta do servidor para seus logs

Defina o nome da conta como uma variável chamada **Account**, em que resourcename é o nome da conta.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Ativar registo

Para habilitar o registro em log, use o cmdlet Set-AzDiagnosticSetting junto com as variáveis para a nova conta de armazenamento, conta de servidor e a categoria. Execute o comando a seguir, definindo o sinalizador **-Enabled** como **$true**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

A saída deverá ter um aspeto semelhante a este exemplo:

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

Essa saída confirma que o log agora está habilitado para o servidor, salvando informações na conta de armazenamento.

Você também pode definir a política de retenção para seus logs para que os logs mais antigos sejam excluídos automaticamente. Por exemplo, defina a política de retenção usando o sinalizador **-RetentionEnabled** como **$true**e o parâmetro set **-RetentionInDays** como **90**. Os logs com mais de 90 dias são excluídos automaticamente.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o [log de diagnóstico de recursos do Azure](../azure-monitor/platform/resource-logs-overview.md).

Consulte [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) na ajuda do PowerShell.
