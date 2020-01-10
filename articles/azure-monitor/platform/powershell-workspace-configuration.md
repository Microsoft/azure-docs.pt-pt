---
title: Utilizar o PowerShell para criar e configurar uma área de trabalho do Log Analytics | Documentos da Microsoft
description: Log Analytics espaços de trabalho no Azure Monitor armazenar dados de servidores em sua infraestrutura local ou na nuvem. Pode recolher dados de máquina de armazenamento do Azure quando gerados pelo diagnóstico do Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/19/2019
ms.openlocfilehash: 68cd0d51c16ecd63a1446c284f81c5dea07b8c06
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75363545"
---
# <a name="manage-log-analytics-workspace-in-azure-monitor-using-powershell"></a>Gerenciar Log Analytics espaço de trabalho no Azure Monitor usando o PowerShell

Você pode usar os [cmdlets do log Analytics PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/) para executar várias funções em um espaço de trabalho Log Analytics no Azure monitor de uma linha de comando ou como parte de um script.  Exemplos das tarefas que pode efetuar com o PowerShell:

* Criar áreas de trabalho
* Adicionar ou remover uma solução
* Importar e exportar pesquisas guardadas
* Criar um grupo de computadores
* Ativar a recolha de registos do IIS de computadores com o agente de Windows instalado
* Recolher contadores de desempenho de computadores com Linux e Windows
* Recolher eventos do syslog em computadores com Linux
* Recolher eventos de registos de eventos do Windows
* Recolher registos de eventos personalizados
* Adicionar o log analytics agent para uma máquina virtual do Azure
* Configurar o log analytics para dados de índice recolhidos através dos diagnósticos do Azure

Este artigo fornece dois exemplos de código que mostram algumas das funções que pode efetuar a partir do PowerShell.  Pode consultar o [referência de cmdlets do PowerShell do Log Analytics](https://docs.microsoft.com/powershell/module/az.operationalinsights/) para outras funções.

> [!NOTE]
> O log Analytics anteriormente chamado das informações operacionais, razão pela qual é o nome utilizado nos cmdlets.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
Esses exemplos funcionam com a versão 1.0.0 ou posterior do módulo AZ. OperationalInsights.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Criar e configurar uma área de trabalho do Log Analytics
O script de exemplo seguinte ilustra como:

1. Criar áreas de trabalho
2. Listar as soluções disponíveis
3. Adicionar soluções para a área de trabalho
4. Pesquisas guardada de importação
5. Pesquisas guardada de exportação
6. Criar um grupo de computadores
7. Ativar a recolha de registos do IIS de computadores com o agente de Windows instalado
8. Recolher contadores de desempenho disco lógico de computadores Linux (% de Inodes utilizados; Megabytes livres; % De espaço; utilizado Transferências/seg do disco; Leituras de disco/seg; Escritas de disco/seg)
9. Recolher eventos do syslog de computadores Linux
10. Recolher eventos de erro e aviso de Log de eventos de computadores Windows
11. Recolher contador de desempenho de memória utilizada em Mbytes disponíveis a partir de computadores Windows
12. Recolher um registo personalizado

```powershell

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique across all Azure subscriptions - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Perf | where ObjectName == "LogicalDisk" and CounterName == "Current Disk Queue Length" and InstanceName == "C:"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1",
    "description": "Example custom log datasource",
    "inputs": [
        {
            "location": {
            "fileSystemLocations": {
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ],
                "linuxFileTypeLogPaths": [ "/var/logs" ]
                }
            },
        "recordDelimiter": {
            "regexDelimiter": {
                "pattern": "\\n",
                "matchIndex": 0,
                "matchIndexSpecified": true,
                "numberedGroup": null
                }
            }
        }
    ],
    "extractions": [
        {
            "extractionName": "TimeGenerated",
            "extractionType": "DateTime",
            "extractionProperties": {
                "dateTimeExtraction": {
                    "regex": null,
                    "joinStringRegex": null
                    }
                }
            }
        ]
    }
"@

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json

# Create Computer Group based on a query
New-AzOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzOperationalInsightsLinuxPerformanceCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernel syslog collection"
Enable-AzOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs

New-AzOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

> [!NOTE]
> O formato do parâmetro **CustomLogRawJson** que define a configuração de um log personalizado pode ser complexo. Use [Get-AzOperationalInsightsDataSource](https://docs.microsoft.com/powershell/module/az.operationalinsights/get-azoperationalinsightsdatasource?view=azps-3.2.0) para recuperar a configuração de um log personalizado existente. A propriedade **Properties** é a configuração necessária para o parâmetro **CustomLogRawJson** .

No exemplo acima, regexDelimiter foi definido como "\\n" para nova linha. O delimitador de log também pode ser um carimbo de data/hora.  Estes são os formatos com suporte:

| Formato | O formato JSON RegEx usa dois \\ para cada \ em um RegEx padrão, portanto, se o teste em um aplicativo RegEx reduzir \\ para \ | | |
| --- | --- | --- | --- |
| `YYYY-MM-DD HH:MM:SS` | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |
| `M/D/YYYY HH:MM:SS AM/PM` | `(([0-1]\\d)|[0-9])/(([0-3]\\d)|(\\d))/((\\d{2})|(\\d{4}))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]\\s(AM|PM|am|pm)` | | |
| `dd/MMM/yyyy HH:MM:SS` | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|May|Apr|Jul|Jun|Aug|Oct|Sep|Nov|Dec|jan|feb|mar|may|apr|jul|jun|aug|oct|sep|nov|dec)\\/((19|20)[0-9][0-9]))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9])` |
| `MMM dd yyyy HH:MM:SS` | `(((?:Jan(?:uary)?|Feb(?:ruary)?|Mar(?:ch)?|Apr(?:il)?|May|Jun(?:e)?|Jul(?:y)?|Aug(?:ust)?|Sep(?:tember)?|Sept|Oct(?:ober)?|Nov(?:ember)?|Dec(?:ember)?)).*?((?:(?:[0-2]?\\d{1})|(?:[3][01]{1})))(?![\\d]).*?((?:(?:[1]{1}\\d{1}\\d{1}\\d{1})|(?:[2]{1}\\d{3})))(?![\\d]).*?((?:(?:[0-1][0-9])|(?:[2][0-3])|(?:[0-9])):(?:[0-5][0-9])(?::[0-5][0-9])?(?:\\s?(?:am|AM|pm|PM))?))` | | |
| `yyMMdd HH:mm:ss` | `([0-9]{2}([0][1-9]|[1][0-2])([0-2][0-9]|[3][0-1])\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `ddMMyy HH:mm:ss` | `(([0-2][0-9]|[3][0-1])([0][1-9]|[1][0-2])[0-9]{2}\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s?([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0-1]?[0-9]|[2][0-3]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM  d HH:mm:ss` <br> dois espaços após MMM | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> Onde + é + ou a- <br> ZZZZ de tempo de deslocamento | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\/((19|20)[0-9][0-9]):([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\s[\\+|\\-][0-9]{4})` | | |
| `yyyy-MM-ddTHH:mm:ss` <br> T é uma letra literal T | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))T((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |

## <a name="configuring-log-analytics-to-send-azure-diagnostics"></a>Configurando Log Analytics para enviar o diagnóstico do Azure
Para a monitorização sem agente de recursos do Azure, os recursos tem de ter o diagnóstico do Azure ativada e configurada para gravar numa área de trabalho do Log Analytics. Essa abordagem envia dados diretamente para o espaço de trabalho e não requer que os dados sejam gravados em uma conta de armazenamento. Os recursos suportados incluem:

| Tipo de Recurso | Registos | Métricas |
| --- | --- | --- |
| Gateways da Aplicação    | Sim | Sim |
| Contas de automatização     | Sim | |
| Contas de Batch          | Sim | Sim |
| Data Lake analytics     | Sim | |
| Arquivo do Data Lake         | Sim | |
| Conjunto elástico de SQL        |     | Sim |
| Espaço de nomes do hub de eventos     |     | Sim |
| Hubs IoT                |     | Sim |
| Key Vault               | Sim | |
| Balanceadores de Carga          | Sim | |
| Logic Apps              | Sim | Sim |
| Grupos de Segurança de Rede | Sim | |
| Cache do Azure para Redis             |     | Sim |
| Procurar serviços         | Sim | Sim |
| Espaço de nomes do Service Bus   |     | Sim |
| SQL (v12)               |     | Sim |
| Web Sites               |     | Sim |
| Farms de servidores Web        |     | Sim |

Para obter os detalhes das métricas disponíveis, consulte [suportado métricas com o Azure Monitor](../../azure-monitor/platform/metrics-supported.md).

Para obter os detalhes dos logs disponíveis, consulte [serviços e esquema com suporte para logs de recursos](../../azure-monitor/platform/diagnostic-logs-schema.md).

```powershell
$workspaceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Também pode utilizar o cmdlet anterior para recolher registos de recursos que estão em subscrições diferentes. O cmdlet é capaz de trabalhar entre assinaturas, pois você está fornecendo a ID do recurso de criação de logs e o espaço de trabalho ao qual os logs são enviados.


## <a name="configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage"></a>Configurando Log Analytics espaço de trabalho para coletar o diagnóstico do Azure do armazenamento
Para recolher dados de registos de dentro de uma instância em execução de um serviço cloud clássico ou um cluster do service fabric, precisa primeiro escrever os dados ao armazenamento do Azure. Um espaço de trabalho Log Analytics é então configurado para coletar os logs da conta de armazenamento. Os recursos suportados incluem:

* Serviços de cloud clássico (funções web e de trabalho)
* Clusters do Service fabric

A exemplo a seguir mostra como:

1. Listar as contas de armazenamento e os locais existentes dos quais o espaço de trabalho indexará dados
2. Criar uma configuração para ler a partir de uma conta de armazenamento
3. Atualizar a configuração do recentemente criada para dados de índice a partir de localizações adicionais
4. Eliminar a configuração criada recentemente

```powershell
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable"
$workspace = (Get-AzOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want the workspace to index
$storageId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight"

```

Também pode utilizar o script anterior para recolher registos de contas de armazenamento em subscrições diferentes. O script é capaz de trabalhar entre assinaturas, pois você está fornecendo a ID de recurso da conta de armazenamento e uma chave de acesso correspondente. Quando alterar a chave de acesso, tem de atualizar a informação de armazenamento para que a nova chave.


## <a name="next-steps"></a>Passos seguintes
* [Reveja o Log Analytics dos cmdlets PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/) para obter mais informações sobre como utilizar o PowerShell para a configuração do Log Analytics.

