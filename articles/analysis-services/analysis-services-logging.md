---
title: Registo de diagnóstico para serviços de análise Azure | Microsoft Docs
description: Descreve como configurar o início de sessão para monitorizar o servidor Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8ede7572079b6a54672234cbf9fe1445dafbad7b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769216"
---
# <a name="setup-diagnostic-logging"></a>Configurar registo de diagnósticos

Uma parte importante de qualquer solução de Serviços de Análise é monitorizar o desempenho dos seus servidores. Os serviços de Análise Azure estão integrados com o Azure Monitor. Com [registos de recursos do Azure Monitor,](../azure-monitor/essentials/platform-logs-overview.md)pode monitorizar e enviar registos para [o Azure Storage,](https://azure.microsoft.com/services/storage/)transmiti-los para [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), e exportá-los para [registos do Azure Monitor](../azure-monitor/overview.md).

![Registo de registo de recursos para registos de armazenamento, centros de eventos ou azure monitor](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>O que é registado?

Pode selecionar categorias **de Motor,** **Serviço** e **Métricas.**

### <a name="engine"></a>Motor

Selecionando registos **de motores** todos [os xEvents](/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Não é possível selecionar eventos individuais. 

|Categorias XEvent |Nome do evento  |
|---------|---------|
|Auditoria de Segurança    |   Início de Sessão de Auditoria      |
|Auditoria de Segurança    |   Fim de Sessão de Auditoria      |
|Auditoria de Segurança    |   O servidor de auditoria começa e para      |
|Relatórios de Progresso     |   Relatório de Progresso Começa      |
|Relatórios de Progresso     |   Relatório de Progresso Termina      |
|Relatórios de Progresso     |   Relatório de progresso Corrente      |
|Consultas     |  Início da Consulta       |
|Consultas     |   Fim da Consulta      |
|Comandos     |  Início do Comando       |
|Comandos     |  Fim do Comando       |
|Erros & Avisos     |   Erro      |
|Detetar     |   Descubra o Fim      |
|Notificação     |    Notificação     |
|Sessão     |  Inicialização de Sessão       |
|Bloqueios    |  Impasse       |
|Processamento de consultas     |   VertiPaq SE Consulta Início      |
|Processamento de consultas     |   VertiPaq SE Consulta Fim      |
|Processamento de consultas     |   VertiPaq SE Consulta Cache Match      |
|Processamento de consultas     |   Início da Consulta Direta      |
|Processamento de consultas     |  Fim de Consulta Direta       |

### <a name="service"></a>Serviço

|Nome da operação  |Ocorre quando  |
|---------|---------|
|ResumeServer     |    Retomar um servidor     |
|SuspendServer    |   Pausa num servidor      |
|DeleteServer     |    Excluir um servidor     |
|RestartServer    |     O utilizador reinicia um servidor através de SSMS ou PowerShell    |
|ObterServerLogFiles    |    Utilizador exporta registo de servidor através do PowerShell     |
|ExportaçãoModel     |   Utilizador exporta um modelo no portal utilizando Open in Visual Studio     |

### <a name="all-metrics"></a>Todas as métricas

A categoria Métrica regista as mesmas [métricas do Servidor](analysis-services-monitor.md#server-metrics) para a tabela AzureMetrics. Se estiver a utilizar a [escala de](analysis-services-scale-out.md) consulta e precisar de separar métricas para cada réplica lida, utilize a tabela AzureDiagnostics, onde **a OperaçãoName** é igual a **LogMetric**.

## <a name="setup-diagnostics-logging"></a>Configurar o registo de diagnósticos

### <a name="azure-portal"></a>Portal do Azure

1. No [portal Azure](https://portal.azure.com) > servidor, clique nas **definições de Diagnóstico** na navegação à esquerda e, em seguida, clique em Ligar os **diagnósticos**.

    ![Ligue a registo de recursos para Azure Cosmos DB no portal Azure](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. Nas **Definições de diagnóstico**, especifique as seguintes opções: 

    * **Nome**. Insira um nome para os registos a criar.

    * **Arquivar para uma conta de armazenamento.** Para utilizar esta opção, precisa de uma conta de armazenamento existente para se ligar. Ver [Criar uma conta de armazenamento](../storage/common/storage-account-create.md). Siga as instruções para criar um Gestor de Recursos, conta de uso geral, e, em seguida, selecione a sua conta de armazenamento devolvendo a esta página no portal. Pode demorar alguns minutos para as contas de armazenamento recentemente criadas aparecerem no menu pendente.
    * **Transmita para um centro de eventos.** Para utilizar esta opção, precisa de um espaço de nomes e de um centro de eventos existente para se conectar. Para obter mais informações, veja [Criar um espaço de nomes de Hubs de Eventos e um hub de eventos com o portal do Azure](../event-hubs/event-hubs-create.md). Em seguida, volte a esta página no portal para selecionar o espaço de nome do Event Hub e o nome da política.
    * **Enviar para O Monitor Azure (Log Analytics workspace)**. Para utilizar esta opção, utilize um espaço de trabalho existente ou crie um novo recurso [de espaço de trabalho](../azure-monitor/logs/quick-create-workspace.md) no portal. Para obter mais informações sobre a visualização dos seus registos, consulte [os registos no espaço de trabalho log Analytics](#view-logs-in-log-analytics-workspace) neste artigo.

    * **Motor**. Selecione esta opção para registar xEvents. Se estiver a arquivar uma conta de armazenamento, pode selecionar o período de retenção para os registos de recursos. Os registos são automaticamente desatados após o termo do período de retenção.
    * **Serviço**. Selecione esta opção para registar eventos de nível de serviço. Se estiver a arquivar uma conta de armazenamento, pode selecionar o período de retenção para os registos de recursos. Os registos são automaticamente desatados após o termo do período de retenção.
    * **Métricas**. Selecione esta opção para armazenar dados verbosos em [Métricas](analysis-services-monitor.md#server-metrics). Se estiver a arquivar uma conta de armazenamento, pode selecionar o período de retenção para os registos de recursos. Os registos são automaticamente desatados após o termo do período de retenção.

3. Clique em **Guardar**.

    Se receber um erro que diga "Falhou na atualização dos diagnósticos para \<workspace name> . A subscrição \<subscription id> não está registada para utilizar microsoft.insights." Siga as instruções [de Troubleshoot Azure Diagnostics](../azure-monitor/essentials/resource-logs.md) para registar a conta e, em seguida, recomprê-lo neste procedimento.

    Se quiser alterar a forma como os seus registos de recursos são guardados em qualquer ponto do futuro, pode voltar a esta página para modificar as definições.

### <a name="powershell"></a>PowerShell

Aqui estão as ordens básicas para te pôr a andar. Se quiser ajuda passo a passo na configuração do registo de uma conta de armazenamento utilizando o PowerShell, consulte o tutorial mais tarde neste artigo.

Para permitir a métrica e a tomada de registo de recursos utilizando o PowerShell, utilize os seguintes comandos:

- Para permitir o armazenamento de registos de recursos numa conta de armazenamento, utilize este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   O ID da conta de armazenamento é o ID do recurso da conta de armazenamento para onde pretende enviar os registos.

- Para permitir o streaming de registos de recursos para um centro de eventos, utilize este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   O ID da regra do Azure Service Bus é uma cadeia de caracteres com este formato:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Para ativar o envio de registos de recursos para um espaço de trabalho do Log Analytics, utilize este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Para obter o ID do recurso da área de trabalho do Log Analytics, utilize o seguinte comando:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Pode combinar estes parâmetros para ativar várias opções de saída.

### <a name="rest-api"></a>API REST

Saiba como [alterar as definições de diagnóstico ao utilizar a API REST do Azure Monitor](/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Modelo do Resource Manager

Saiba como [ativar as definições de diagnóstico durante a criação de recursos com um modelo do Resource Manager](../azure-monitor/essentials/resource-manager-diagnostic-settings.md). 

## <a name="manage-your-logs"></a>Gerir os registos

Os registos estão normalmente disponíveis dentro de algumas horas após a instalação de registo. Cabe-lhe gerir os registos na sua conta de armazenamento:

* Utilize métodos de controlo de acesso do Azure standard para proteger os registos ao restringir quem pode aceder a eles.
* Elimine registos que já não pretende manter na conta de armazenamento.
* Certifique-se de definir um período de retenção para que os registos antigos sejam eliminados da sua conta de armazenamento.

## <a name="view-logs-in-log-analytics-workspace"></a>Ver registos no espaço de trabalho log Analytics

As métricas e os eventos de servidor são integrados com xEvents no seu recurso de espaço de trabalho Log Analytics para análise lado a lado. O log Analytics workspace também pode ser configurado para receber eventos de outros serviços Azure fornecendo uma visão holística de dados de registo de diagnóstico em toda a sua arquitetura.

Para visualizar os seus dados de diagnóstico, no espaço de trabalho Log Analytics, abra **os Registos**  a partir do menu esquerdo.

![Registar opções de pesquisa no portal Azure](./media/analysis-services-logging/aas-logging-open-log-search.png)

No construtor de consultas, **expanda o LogManagement**  >  **AzureDiagnostics**. A AzureDiagnostics inclui eventos de motor e serviço. Note que uma consulta é criada no voo. O campo EventClass \_ contém nomes xEvent, que podem parecer familiares se tiver usado xEvents para registo de registo no local. Clique em **EventClass \_ s** ou num dos nomes do evento e o espaço de trabalho Log Analytics continua a construir uma consulta. Confirme que guarda as consultas para reutilização posterior.

### <a name="example-queries"></a>Consultas de exemplo

#### <a name="example-1"></a>Exemplo 1

As seguintes durações de consulta devolvem as durações de cada evento final de consulta final/atualização para uma base de dados e servidor de modelos. Se dimensionado, os resultados são divididos por réplica porque o número da réplica está incluído em ServerName_s. O agrupamento por RootActivityId_g reduz a contagem de linhas recuperada da AZure Diagnostics REST API e ajuda a manter-se dentro dos limites descritos nos [limites da Taxa de Análise de Registos](https://dev.loganalytics.io/documentation/Using-the-API/Limits).

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

A seguinte consulta devolve memória e consumo de QPU para um servidor. Se dimensionado, os resultados são divididos por réplica porque o número da réplica está incluído em ServerName_s.

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

A seguinte consulta devolve os contadores de desempenho do motor de serviços de leitura/sec rows para um servidor.

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

Há centenas de consultas que pode utilizar. Para saber mais sobre consultas, consulte Começar com consultas de registo do [Azure Monitor](../azure-monitor/logs/get-started-queries.md).


## <a name="turn-on-logging-by-using-powershell"></a>Ligue o registo através da utilização do PowerShell

Neste tutorial rápido, cria uma conta de armazenamento no mesmo grupo de subscrição e recursos que o servidor do Serviço de Análise. Em seguida, utiliza-se Set-AzDiagnosticSetting para ligar o registo de diagnósticos, enviando a saída para a nova conta de armazenamento.

### <a name="prerequisites"></a>Pré-requisitos
Para completar este tutorial, deve ter os seguintes recursos:

* Um servidor de Serviços de Análise Azure existente. Para obter instruções sobre a criação de um recurso de servidor, consulte [Criar um servidor no portal Azure](analysis-services-create-server.md)ou criar um servidor de [Serviços de Análise Azure utilizando o PowerShell](analysis-services-create-powershell.md).

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

Em seguida, para especificar a subscrição que está associada à conta Azure Analysis Services que está a registar, escreva:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Se tiver várias subscrições associadas à sua conta, é importante especificar a subscrição.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Criar uma nova conta de armazenamento para os seus registos

Pode utilizar uma conta de armazenamento existente para os seus registos, desde que esteja na mesma subscrição que o seu servidor. Para este tutorial, cria uma nova conta de armazenamento dedicada aos registos dos Serviços de Análise. Para facilitar, está a armazenar os detalhes da conta de armazenamento numa variável chamada **sa.**

Também utiliza o mesmo grupo de recursos que o que contém o servidor 'Serviços de Análise'. Valores de substituição `awsales_resgroup` `awsaleslogs` para, e `West Central US` com os seus próprios valores:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identifique a conta do servidor para os seus registos

Desaprote o nome da conta para uma **conta** com o nome variável, onde o Nome de Recursos é o nome da conta.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Ativar registo

Para ativar o registo, utilize o Set-AzDiagnosticSetting cmdlet juntamente com as variáveis para a nova conta de armazenamento, conta de servidor e categoria. Executar o seguinte comando, definindo a bandeira **ativada** para **$true:**

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

Também pode definir a política de retenção para os seus registos para que os registos mais antigos sejam automaticamente eliminados. Por exemplo, definir a política de retenção utilizando a bandeira **-RetentionEnabled** para **$true**, e definir **-RetençãoInDays** parâmetro para **90**. Os registos com mais de 90 dias são automaticamente eliminados.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a registo de recursos do Azure Monitor.](../azure-monitor/essentials/platform-logs-overview.md)

Consulte [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) na ajuda PowerShell.
