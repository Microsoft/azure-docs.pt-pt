---
title: Usar o PowerShell para criar e configurar um espaço de trabalho Log Analytics | Microsoft Docs
description: Log Analytics espaços de trabalho no Azure Monitor armazenar dados de servidores em sua infraestrutura local ou na nuvem. Você pode coletar dados do computador do armazenamento do Azure quando gerados pelo diagnóstico do Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/19/2019
ms.openlocfilehash: 9d5bbaf02798c0fd87c40f1d952db19aac7b0b7e
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932085"
---
# <a name="manage-log-analytics-workspace-in-azure-monitor-using-powershell"></a>Gerenciar Log Analytics espaço de trabalho no Azure Monitor usando o PowerShell

Você pode usar os [cmdlets do log Analytics PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/) para executar várias funções em um espaço de trabalho Log Analytics no Azure monitor de uma linha de comando ou como parte de um script.  Exemplos das tarefas que você pode executar com o PowerShell incluem:

* Criar áreas de trabalho
* Adicionar ou remover uma solução
* Importar e exportar pesquisas salvas
* Criar um grupo de computadores
* Habilitar a coleta de logs do IIS de computadores com o agente do Windows instalado
* Coletar contadores de desempenho de computadores Linux e Windows
* Coletar eventos do syslog em computadores Linux
* Coletar eventos de logs de eventos do Windows
* Coletar logs de eventos personalizados
* Adicionar o agente do log Analytics a uma máquina virtual do Azure
* Configurar o log Analytics para indexar dados coletados usando o diagnóstico do Azure

Este artigo fornece dois exemplos de código que ilustram algumas das funções que você pode executar do PowerShell.  Você pode consultar o [log Analytics referência de cmdlet do PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/) para outras funções.

> [!NOTE]
> O Log Analytics anteriormente era chamado de insights operacionais, motivo pelo qual ele é o nome usado nos cmdlets.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
Esses exemplos funcionam com a versão 1.0.0 ou posterior do módulo AZ. OperationalInsights.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Criar e configurar um espaço de trabalho Log Analytics
O exemplo de script a seguir ilustra como:

1. Criar áreas de trabalho
2. Listar as soluções disponíveis
3. Adicionar soluções ao espaço de trabalho
4. Importar pesquisas salvas
5. Exportar pesquisas salvas
6. Criar um grupo de computadores
7. Habilitar a coleta de logs do IIS de computadores com o agente do Windows instalado
8. Coletar contadores de desempenho de disco lógico de computadores Linux (% de inodes usados; Megabytes livres; % De espaço usado; Transferências de disco/s; Leituras de disco/s; Gravações de disco/s)
9. Coletar eventos de syslog de computadores Linux
10. Coletar eventos de erro e aviso do log de eventos do aplicativo de computadores com Windows
11. Coletar o contador de desempenho Mbytes disponíveis de memória de computadores Windows
12. Coletar um log personalizado

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
Para o monitoramento sem agente de recursos do Azure, os recursos precisam ter o diagnóstico do Azure habilitado e configurado para gravar em um espaço de trabalho Log Analytics. Essa abordagem envia dados diretamente para o espaço de trabalho e não requer que os dados sejam gravados em uma conta de armazenamento. Os recursos com suporte incluem:

| Tipo de Recurso | Registos | Métricas |
| --- | --- | --- |
| Gateways da Aplicação    | Sim | Sim |
| Contas de automatização     | Sim | |
| Contas do lote          | Sim | Sim |
| Data Lake analytics     | Sim | |
| Data Lake Store         | Sim | |
| Pool SQL elástico        |     | Sim |
| Espaço de nomes do hub de eventos     |     | Sim |
| Hubs IoT                |     | Sim |
| Key Vault               | Sim | |
| Balanceadores de Carga          | Sim | |
| Logic Apps              | Sim | Sim |
| Grupos de Segurança de Rede | Sim | |
| Cache do Azure para Redis             |     | Sim |
| Procurar serviços         | Sim | Sim |
| Namespace do barramento de serviço   |     | Sim |
| SQL (V12)               |     | Sim |
| Web Sites               |     | Sim |
| Farms de servidores Web        |     | Sim |

Para obter os detalhes das métricas disponíveis, consulte [métricas com suporte com Azure monitor](../../azure-monitor/platform/metrics-supported.md).

Para obter os detalhes dos logs disponíveis, consulte [serviços e esquema com suporte para logs de diagnóstico](../../azure-monitor/platform/diagnostic-logs-schema.md).

```powershell
$workspaceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Você também pode usar o cmdlet anterior para coletar logs de recursos que estão em assinaturas diferentes. O cmdlet é capaz de trabalhar entre assinaturas, pois você está fornecendo a ID do recurso de criação de logs e o espaço de trabalho ao qual os logs são enviados.


## <a name="configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage"></a>Configurando Log Analytics espaço de trabalho para coletar o diagnóstico do Azure do armazenamento
Para coletar dados de log de dentro de uma instância em execução de um serviço de nuvem clássico ou de um cluster do Service Fabric, você precisa primeiro gravar os dados no armazenamento do Azure. Um espaço de trabalho Log Analytics é então configurado para coletar os logs da conta de armazenamento. Os recursos com suporte incluem:

* Serviços de nuvem clássicos (funções Web e de trabalho)
* Clusters do Service Fabric

O exemplo a seguir mostra como:

1. Listar as contas de armazenamento e os locais existentes dos quais o espaço de trabalho indexará dados
2. Criar uma configuração para ler de uma conta de armazenamento
3. Atualizar a configuração recém-criada para indexar dados de locais adicionais
4. Excluir a configuração recém-criada

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

Você também pode usar o script anterior para coletar logs de contas de armazenamento em assinaturas diferentes. O script é capaz de trabalhar entre assinaturas, pois você está fornecendo a ID de recurso da conta de armazenamento e uma chave de acesso correspondente. Quando você altera a chave de acesso, precisa atualizar o insight de armazenamento para ter a nova chave.


## <a name="next-steps"></a>Passos seguintes
* [Examine log Analytics cmdlets do PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/) para obter informações adicionais sobre como usar o PowerShell para configuração de log Analytics.

