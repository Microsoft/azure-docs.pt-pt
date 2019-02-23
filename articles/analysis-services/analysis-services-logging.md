---
title: Diagnóstico de registo para o Azure Analysis Services | Documentos da Microsoft
description: Saiba mais sobre como configurar o registo de diagnósticos para o Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b35707b857c66f0f1b91f2f1b5dd7a0ffa24dd9e
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56733763"
---
# <a name="setup-diagnostic-logging"></a>Configurar registo de diagnósticos

Uma parte importante de qualquer solução de Analysis Services está a monitorizar o desempenho dos seus servidores. Com o [registos de diagnóstico de recursos do Azure](../azure-monitor/platform/diagnostic-logs-overview.md), pode monitorizar e enviar registos ao [armazenamento do Azure](https://azure.microsoft.com/services/storage/), transmiti-los para [Hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs/)e exportá-las para [Azure Monitorize registos](../azure-monitor/azure-monitor-log-hub.md).

![Registo de diagnósticos para registos de armazenamento, os Hubs de eventos ou do Azure Monitor](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>O que é registado?

Pode selecionar **motor**, **Service**, e **métricas** categorias.

### <a name="engine"></a>Motor

Selecionando **motor** regista todos [xEvents](https://docs.microsoft.com/sql/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Não é possível selecionar eventos individuais. 

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

A categoria de métricas, registos a mesma [métricas do servidor](analysis-services-monitor.md#server-metrics) apresentados nas métricas.

## <a name="setup-diagnostics-logging"></a>Configurar registo de diagnóstico

### <a name="azure-portal"></a>Portal do Azure

1. Na [portal do Azure](https://portal.azure.com) > servidor, clique em **registos de diagnóstico** na navegação à esquerda e, em seguida, clique **ativar diagnósticos**.

    ![Ativar o registo de diagnósticos para o Azure Cosmos DB no portal do Azure](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. Na **das definições de diagnóstico**, especifique as seguintes opções: 

    * **Nome**. Introduza um nome para os registos criar.

    * **Arquivo para uma conta de armazenamento**. Para utilizar esta opção, terá de uma conta de armazenamento existente para ligar a. Ver [criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md). Siga as instruções para criar um Gerenciador de recursos, conta para fins gerais, em seguida, selecione a sua conta de armazenamento por meio do retorno a esta página no portal. Pode demorar alguns minutos para as contas de armazenamento recentemente criada aparece no menu pendente.
    * **Stream para um hub de eventos**. Para utilizar esta opção, terá de um Hub de eventos espaço de nomes e o event hub para ligar a. Para obter mais informações, consulte [criar um espaço de nomes de Hubs de eventos e um hub de eventos com o portal do Azure](../event-hubs/event-hubs-create.md). Em seguida, regresse a esta página no portal para selecionar o nome de espaço de nomes e a política do Hub de eventos.
    * **Enviar para o Azure Monitor (área de trabalho do Log Analytics)**. Para utilizar esta opção, utilize uma área de trabalho existente ou [criar uma nova área de trabalho](../azure-monitor/learn/quick-create-workspace.md) recursos no portal. Para obter mais informações sobre a visualização de seus registos, consulte [vista de registos na área de trabalho do Log Analytics](#view-logs-in-log-analytics) neste artigo.

    * **Motor**. Selecione esta opção para registar xEvents. Se estiver arquivando para uma conta de armazenamento, pode selecionar o período de retenção para os registos de diagnóstico. Os registos estão autodeleted após o período de retenção expira.
    * **Serviço**. Selecione esta opção para registar eventos de nível de serviço. Se estiver a arquivamento para uma conta de armazenamento, pode selecionar o período de retenção para os registos de diagnóstico. Os registos estão autodeleted após o período de retenção expira.
    * **Métricas**. Selecione esta opção para armazenar dados detalhados no [métricas](analysis-services-monitor.md#server-metrics). Se estiver a arquivamento para uma conta de armazenamento, pode selecionar o período de retenção para os registos de diagnóstico. Os registos estão autodeleted após o período de retenção expira.

3. Clique em **Guardar**.

    Se receber um erro que diz "Falha ao atualizar diagnósticos para \<nome de área de trabalho >. A subscrição \<id da subscrição > não está registada para utilizar o Microsoft. insights. " Siga os [resolver problemas relacionados com o Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) as instruções para registar a conta, em seguida, repita este procedimento.

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

Saiba como [alterar as definições de diagnóstico, utilizando a API de REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Modelo do Resource Manager

Saiba como [ative as definições de diagnóstico durante a criação de recursos com um modelo do Resource Manager](../azure-monitor/platform/diagnostic-logs-stream-template.md). 

## <a name="manage-your-logs"></a>Gerir os seus registos

Os registos estão normalmente disponíveis dentro de duas horas de configuração de registo. Cabe-lhe gerir os seus registos na sua conta de armazenamento:

* Utilize métodos padrão de controlo de acesso do Azure para proteger os seus registos, restringindo o seu acesso.
* Elimine os registos que já não pretende manter na sua conta de armazenamento.
* Certifique-se de que definir um período de retenção para que os registos antigos serão eliminados da conta de armazenamento.

## <a name="view-logs-in-log-analytics-workspace"></a>Ver registos na área de trabalho do Log Analytics

Eventos de métricas e servidor estão integrados xEvents no seu recurso da área de trabalho do Log Analytics para análise de lado a lado. Área de trabalho do log Analytics também pode ser configurada para receber eventos dos outros serviços do Azure, oferecendo uma visão holística dos dados de registo de diagnóstico em sua arquitetura.

Para ver os dados de diagnóstico, na área de trabalho do Log Analytics, abra **registos** no menu à esquerda.

![Opções de pesquisa de registo no portal do Azure](./media/analysis-services-logging/aas-logging-open-log-search.png)

No construtor de consultas, expanda **LogManagement** > **AzureDiagnostics**. AzureDiagnostics inclui o mecanismo e eventos de serviço. Tenha em atenção que é criada uma consulta em tempo real. A registar\_campo s contém nomes de xEvent, que podem parecer familiares se já usou xEvents para iniciar sessão no local. Clique em **registar\_s** ou um dos nomes de eventos e área de trabalho do Log Analytics continua a construir uma consulta. Certifique-se de que guardar as suas consultas para reutilização posterior.

### <a name="example-query"></a>Consulta de exemplo
Esta consulta calcula e devolve a CPU para cada evento de final de fim/atualização de consulta para uma base de dados do modelo e o servidor:

```Kusto
let window =  AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and ServerName_s =~"MyServerName" and DatabaseName_s == "Adventure Works Localhost" ;
window
| where OperationName has "QueryEnd" or (OperationName has "CommandEnd" and EventSubclass_s == 38)
| where extract(@"([^,]*)", 1,Duration_s, typeof(long)) > 0
| extend DurationMs=extract(@"([^,]*)", 1,Duration_s, typeof(long))
| extend Engine_CPUTime=extract(@"([^,]*)", 1,CPUTime_s, typeof(long))
| project  StartTime_t,EndTime_t,ServerName_s,OperationName,RootActivityId_g ,TextData_s,DatabaseName_s,ApplicationName_s,Duration_s,EffectiveUsername_s,User_s,EventSubclass_s,DurationMs,Engine_CPUTime
| join kind=leftouter (
window
    | where OperationName == "ProgressReportEnd" or (OperationName == "VertiPaqSEQueryEnd" and EventSubclass_s  != 10) or OperationName == "DiscoverEnd" or (OperationName has "CommandEnd" and EventSubclass_s != 38)
    | summarize sum_Engine_CPUTime = sum(extract(@"([^,]*)", 1,CPUTime_s, typeof(long))) by RootActivityId_g
    ) on RootActivityId_g
| extend totalCPU = sum_Engine_CPUTime + Engine_CPUTime

```


Há centenas de consultas que pode utilizar. Para saber mais sobre consultas, veja [introdução às consultas de registo do Azure Monitor](../azure-monitor/log-query/get-started-queries.md).


## <a name="turn-on-logging-by-using-powershell"></a>Ativar o registo com o PowerShell

Este tutorial rápido, vai criar uma conta de armazenamento na mesma subscrição e grupo de recursos que o seu servidor do serviço de análise. Em seguida, utilizar Set-AzDiagnosticSetting para ativar os diagnósticos de log e enviar a saída para a nova conta de armazenamento.

### <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, tem de ter os seguintes recursos:

* Um servidor existente do Azure Analysis Services. Para obter instruções sobre como criar um recurso de servidor, consulte [criar um servidor no portal do Azure](analysis-services-create-server.md), ou [criar um servidor Azure Analysis Services com o PowerShell](analysis-services-create-powershell.md).

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

Pode utilizar uma conta de armazenamento existente para os seus registos, desde que ele está na mesma subscrição que o seu servidor. Para este tutorial, crie uma nova conta de armazenamento dedicada aos registos de Analysis Services. Para que seja fácil, está armazenando os detalhes da conta de armazenamento numa variável chamada **sa**.

Também usar o mesmo grupo de recursos como aquela que contém o servidor do Analysis Services. Substitua os valores para `awsales_resgroup`, `awsaleslogs`, e `West Central US` pelos seus próprios valores:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identificar a conta de servidor para os seus registos

Defina o nome de conta como uma variável chamada **conta**, onde ResourceName é o nome da conta.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Ativar registo

Para ativar o registo, utilize o cmdlet Set-AzDiagnosticSetting juntamente com as variáveis para a nova conta de armazenamento, conta de servidor e a categoria. Execute o seguinte comando, definindo a **-ativado** sinalizador para **$true**:

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

Esta saída confirma que o registo está agora ativado para o servidor, ao guardar as informações para a conta de armazenamento.

Também pode definir a política de retenção para os seus registos, para que os registos mais antigos são automaticamente eliminados. Por exemplo, definir a política de retenção utilizando **- RetentionEnabled** sinalizador para **$true**e defina **- RetentionInDays** parâmetro **90**. Registos de mais de 90 dias são automaticamente eliminados.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [registo de diagnósticos de recursos do Azure](../azure-monitor/platform/diagnostic-logs-overview.md).

Ver [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.insights/Set-azDiagnosticSetting) na ajuda do PowerShell.
