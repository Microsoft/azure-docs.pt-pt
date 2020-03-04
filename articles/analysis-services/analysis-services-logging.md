---
title: Exploração madeireira de diagnóstico para serviços de análise azure  Microsoft Docs
description: Descreve como configurar a exploração de diagnóstico de recursos Azure para monitorizar o servidor dos Serviços de Análise Do Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0f13f297facedceb50920c0f6afca63fe1df0b48
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78248057"
---
# <a name="setup-diagnostic-logging"></a>Configurar registo de diagnósticos

Uma parte importante de qualquer solução de Analysis Services está a monitorizar o desempenho dos seus servidores. Com registos de [recursos Azure,](../azure-monitor/platform/platform-logs-overview.md)pode monitorizar e enviar registos para [o Azure Storage,](https://azure.microsoft.com/services/storage/)transmiti-los para os Hubs de [Eventos Azure,](https://azure.microsoft.com/services/event-hubs/)e exportá-los para [registos do Monitor Azure.](../azure-monitor/azure-monitor-log-hub.md)

![Registos de diagnóstico de armazenamento, centros de eventos ou registos do Monitor Azure](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>O que é registado?

Pode selecionar categorias de **Motor,** **Serviço**e **Métricas.**

### <a name="engine"></a>Motor

Selecionar registos **do motor** todos [os xEvents](https://docs.microsoft.com/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Não é possível selecionar eventos individuais. 

|Categorias de XEvent |Nome do evento  |
|---------|---------|
|Auditoria de Segurança    |   Início de sessão de auditoria      |
|Auditoria de Segurança    |   Fim de sessão de auditoria      |
|Auditoria de Segurança    |   Servidor de auditoria é iniciada e interrompida      |
|Relatórios de progresso     |   Início do relatório de progresso      |
|Relatórios de progresso     |   Fim do relatório de progresso      |
|Relatórios de progresso     |   Atual do relatório de progresso      |
|Consultas     |  Início da consulta       |
|Consultas     |   Fim da consulta      |
|Comandos     |  Início do comando       |
|Comandos     |  Fim do comando       |
|Erros e avisos     |   Erro      |
|Descobrir     |   Detetar final      |
|Notificação     |    Notificação     |
|Sessão     |  Inicialização de sessão       |
|Bloqueios    |  Deadlock       |
|Processamento de consultas     |   Início de consulta SE VertiPaq      |
|Processamento de consultas     |   VertiPaq SE Query End      |
|Processamento de consultas     |   Correspondência de Cache de consulta SE VertiPaq      |
|Processamento de consultas     |   Início da consulta direta      |
|Processamento de consultas     |  Fim da consulta direta       |

### <a name="service"></a>Serviço

|Nome da operação  |Ocorre quando  |
|---------|---------|
|ResumeServer     |    Retomar a um servidor     |
|SuspendServer    |   Colocar em pausa um servidor      |
|DeleteServer     |    Eliminar um servidor     |
|RestartServer    |     Utilizador reinicia um servidor através do SSMS ou o PowerShell    |
|GetServerLogFiles    |    Utilizador exporta o registo do servidor através do PowerShell     |
|ExportModel     |   Utilizador exporta um modelo no portal usando aberto no Visual Studio     |

### <a name="all-metrics"></a>Todas as métricas

A categoria Métricas regista as mesmas [métricas do Servidor](analysis-services-monitor.md#server-metrics) para a tabela AzureMetrics. Se estiver a utilizar a [escala de](analysis-services-scale-out.md) consulta e precisar de separar métricas para cada réplica de leitura, utilize a tabela AzureDiagnostics, onde o Nome **da Operação** é igual ao **LogMetric**.

## <a name="setup-diagnostics-logging"></a>Configurar o registo de diagnósticos

### <a name="azure-portal"></a>Portal do Azure

1. No [portal EI](https://portal.azure.com) e no servidor, clique em **registos de diagnóstico** na navegação à esquerda e, em seguida, clique em Ligar **diagnósticos**.

    ![Ativar o registo de diagnósticos para o Azure Cosmos DB no portal do Azure](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. Nas **definições de Diagnóstico,** especifique as seguintes opções: 

    * **Nome**. Introduza um nome para os registos criar.

    * **Arquivar para uma conta de armazenamento.** Para utilizar esta opção, terá de uma conta de armazenamento existente para ligar a. Ver [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md). Siga as instruções para criar um Gerenciador de recursos, conta para fins gerais, em seguida, selecione a sua conta de armazenamento por meio do retorno a esta página no portal. Pode demorar alguns minutos para as contas de armazenamento recentemente criada aparece no menu pendente.
    * **Stream para um centro de eventos.** Para utilizar esta opção, terá de um Hub de eventos espaço de nomes e o event hub para ligar a. Para saber mais, consulte [Create a Event Hubs namespace e um hub de eventos utilizando o portal Azure](../event-hubs/event-hubs-create.md). Em seguida, regresse a esta página no portal para selecionar o nome de espaço de nomes e a política do Hub de eventos.
    * **Enviar para o Monitor Azure (log analytics workspace)** . Para utilizar esta opção, utilize um espaço de trabalho existente ou [crie um novo](../azure-monitor/learn/quick-create-workspace.md) recurso espaço de trabalho no portal. Para mais informações sobre a visualização dos seus registos, consulte [os registos no espaço](#view-logs-in-log-analytics-workspace) de trabalho do Log Analytics neste artigo.

    * **Motor.** Selecione esta opção para registar xEvents. Se estiver arquivando para uma conta de armazenamento, pode selecionar o período de retenção para os registos de diagnóstico. Os registos estão autodeleted após o período de retenção expira.
    * **Serviço.** Selecione esta opção para registar eventos de nível de serviço. Se estiver a arquivamento para uma conta de armazenamento, pode selecionar o período de retenção para os registos de diagnóstico. Os registos estão autodeleted após o período de retenção expira.
    * **Métricas.** Selecione esta opção para armazenar dados verbosos em [Métricas](analysis-services-monitor.md#server-metrics). Se estiver a arquivamento para uma conta de armazenamento, pode selecionar o período de retenção para os registos de diagnóstico. Os registos estão autodeleted após o período de retenção expira.

3. Clique em **Guardar**.

    Se receber um erro que diga "Falhei em atualizar diagnósticos para \<nome do espaço de trabalho>. A subscrição \<subscrição id> não está registada para usar microsoft.insights." siga as instruções de [Diagnóstico De Troubleshoot Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) para registar a conta e, em seguida, tente novamente este procedimento.

    Se pretender alterar a forma como em que seus registos de diagnóstico são guardados no futuro em qualquer momento, pode regressar a esta página para modificar as definições.

### <a name="powershell"></a>PowerShell

Aqui estão os comandos básicos para o ajudar. Se pretender que o ajuda passo a passo sobre como configurar o registo para uma conta de armazenamento com o PowerShell, veja o tutorial neste artigo.

Para ativar as métricas e diagnósticos de registro com o PowerShell, utilize os seguintes comandos:

- Para ativar o armazenamento de registos de diagnóstico numa conta de armazenamento, utilize este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   O ID de conta de armazenamento é o ID de recurso para a conta de armazenamento onde pretende enviar os registos.

- Para ativar a transmissão em fluxo de registos de diagnóstico para um hub de eventos, use este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   O ID de regra de Azure Service Bus é uma cadeia de caracteres com este formato:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Para ativar o envio de registos de diagnóstico para uma área de trabalho do Log Analytics, use este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Pode obter o ID de recurso da sua área de trabalho do Log Analytics, utilizando o seguinte comando:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Pode combinar estes parâmetros para ativar várias opções de saída.

### <a name="rest-api"></a>API REST

Aprenda a alterar as definições de [diagnóstico utilizando a API REST DO Monitor Azure](https://docs.microsoft.com/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Modelo do Resource Manager

Aprenda a permitir configurações de [diagnóstico na criação](../azure-monitor/platform/diagnostic-settings-template.md)de recursos utilizando um modelo de Gestor de Recursos . 

## <a name="manage-your-logs"></a>Gerir os seus registos

Os registos estão normalmente disponíveis dentro de duas horas de configuração de registo. Cabe-lhe gerir os seus registos na sua conta de armazenamento:

* Utilize métodos padrão de controlo de acesso do Azure para proteger os seus registos, restringindo o seu acesso.
* Elimine os registos que já não pretende manter na sua conta de armazenamento.
* Certifique-se de que definir um período de retenção para que os registos antigos serão eliminados da conta de armazenamento.

## <a name="view-logs-in-log-analytics-workspace"></a>Ver registos no espaço de trabalho do Log Analytics

As métricas e os eventos do servidor estão integrados com xEvents no seu recurso de espaço de trabalho Log Analytics para análise lado a lado. O espaço de trabalho do Log Analytics também pode ser configurado para receber eventos de outros serviços do Azure, fornecendo uma visão holística dos dados de registo de diagnóstico em toda a sua arquitetura.

Para ver os seus dados de diagnóstico, no espaço de trabalho do Log Analytics, abra **registos** do menu esquerdo.

![Opções de pesquisa de registo no portal do Azure](./media/analysis-services-logging/aas-logging-open-log-search.png)

No construtor de consultas, expanda **a LogManagement** > **AzureDiagnostics**. AzureDiagnostics inclui o mecanismo e eventos de serviço. Note que uma consulta é criada no voo. O campo do EventClass\_contém nomes xEvento, que podem parecer familiares se tiver usado xEvents para a exploração no local. Clique em **EventClass\_ou** um dos nomes do evento e o espaço de trabalho log Analytics continua a construir uma consulta. Certifique-se de que guardar as suas consultas para reutilização posterior.

### <a name="example-queries"></a>Consultas de exemplo

#### <a name="example-1"></a>Exemplo 1

A consulta seguinte devolve durações para cada evento final de consulta/atualização para uma base de dados de modeloe servidor. Se forem eliminados, os resultados são desfeito por réplica porque o número da réplica está incluído na ServerName_s. O agrupamento por RootActivityId_g reduz a contagem de filas recuperada da API de Repouso de Diagnósticos Azure e ajuda a manter-se dentro dos limites descritos nos [limites](https://dev.loganalytics.io/documentation/Using-the-API/Limits)da Taxa de Análise de Log .

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

A consulta seguinte devolve a memória e o consumo de QPU para um servidor. Se forem eliminados, os resultados são desfeito por réplica porque o número da réplica está incluído na ServerName_s.

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

A consulta seguinte devolve os contadores de desempenho do motor de análise de Linhas/sec para um servidor.

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

Há centenas de consultas que pode utilizar. Para saber mais sobre consultas, consulte Começar com consultas de [registo do Monitor Azure](../azure-monitor/log-query/get-started-queries.md).


## <a name="turn-on-logging-by-using-powershell"></a>Ativar o registo com o PowerShell

Este tutorial rápido, vai criar uma conta de armazenamento na mesma subscrição e grupo de recursos que o seu servidor do serviço de análise. Em seguida, utilize o Set-AzDiagnosticSetting para ligar o registo de diagnósticos, enviando a saída para a nova conta de armazenamento.

### <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, tem de ter os seguintes recursos:

* Um servidor existente do Azure Analysis Services. Para obter instruções sobre a criação de um recurso de servidor, consulte [Criar um servidor no portal Azure](analysis-services-create-server.md), ou criar um servidor de Serviços de Análise Azure utilizando o [PowerShell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Ligar às suas subscrições

Abra uma sessão no Azure PowerShell e inicie sessão na sua conta do Azure com o seguinte comando:  

```powershell
Connect-AzAccount
```

Na janela pop-up do browser, introduza o seu nome de utilizador da conta do Azure e a palavra-passe. O Azure PowerShell obtém todas as subscrições associadas a esta conta e, por defeito, utiliza a primeira.

Se tiver várias subscrições, poderá ter de especificar uma subscrição utilizada para criar o seu Cofre de Chaves do Azure. Escreva o seguinte para ver as subscrições da sua conta:

```powershell
Get-AzSubscription
```

Em seguida, para especificar a subscrição que está associada a conta do Azure Analysis Services que está a iniciar sessão, escreva:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Se tiver várias subscrições associadas à sua conta, é importante especificar a subscrição.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Criar uma nova conta de armazenamento para os seus registos

Pode utilizar uma conta de armazenamento existente para os seus registos, desde que ele está na mesma subscrição que o seu servidor. Para este tutorial, cria uma nova conta de armazenamento dedicada aos registos dos Serviços de Análise. Para facilitar, está a armazenar os detalhes da conta de armazenamento numa variável chamada **sa**.

Também usar o mesmo grupo de recursos como aquela que contém o servidor do Analysis Services. Valores de substituição de `awsales_resgroup`, `awsaleslogs`e `West Central US` com os seus próprios valores:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identificar a conta de servidor para os seus registos

Detete o nome da conta para uma **conta**com nome variável, onde o Nome do Recurso é o nome da conta.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Ativar o registo

Para ativar o registo, utilize o cmdlet Set-AzDiagnosticSetting, juntamente com as variáveis para a nova conta de armazenamento, conta de servidor e a categoria. Executar o seguinte comando, colocando a bandeira **ativada** para **$true:**

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

Esta saída confirma que o registo está agora ativado para o servidor, guardando informações para a conta de armazenamento.

Também pode definir a política de retenção para os seus registos, para que os registos mais antigos são automaticamente eliminados. Por exemplo, defina a política de retenção utilizando a bandeira **ativada** para **$true**e coloque o parâmetro **-RetençãoInDays** para **90**. Registos de mais de 90 dias são automaticamente eliminados.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a exploração de diagnóstico de [recursos Azure.](../azure-monitor/platform/platform-logs-overview.md)

Consulte o [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) na ajuda PowerShell.
