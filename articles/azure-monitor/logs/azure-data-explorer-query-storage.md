---
title: Consulta dados exportados do Azure Monitor usando Azure Data Explorer (pré-visualização)
description: Utilize o Azure Data Explorer para consultar dados que foram exportados do seu espaço de trabalho Log Analytics para uma conta de armazenamento Azure.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 5eff593075db118b23d74147e33b40eb4402193c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031162"
---
# <a name="query-exported-data-from-azure-monitor-using-azure-data-explorer-preview"></a>Consulta dados exportados do Azure Monitor usando Azure Data Explorer (pré-visualização)
A exportação de dados do Azure Monitor para uma conta de armazenamento Azure permite a retenção de baixo custo e a capacidade de realocar registos para diferentes regiões. Utilize o Azure Data Explorer para consultar dados que foram exportados dos seus espaços de trabalho Log Analytics. Uma vez configuradas, as tabelas suportadas que são enviadas dos seus espaços de trabalho para uma conta de armazenamento Azure estarão disponíveis como fonte de dados para o Azure Data Explorer.

O fluxo de processo é o seguinte: 

1.  Dados de exportação do espaço de trabalho Log Analytics para a conta de armazenamento Azure.
2.  Crie uma tabela externa no seu Cluster Azure Data Explorer e mapeamento para os tipos de dados.
3.  Consulta de dados do Azure Data Explorer.

:::image type="content" source="media/azure-data-explorer-query-storage/exported-data-query.png" alt-text="O Azure Data Explorer exportou o fluxo de consulta de dados.":::



## <a name="send-data-to-azure-storage"></a>Enviar dados para o armazenamento da Azure
Os registos do Azure Monitor podem ser exportados para uma Conta de Armazenamento Azure utilizando qualquer uma das seguintes opções.

- Para exportar todos os dados do seu espaço de trabalho Log Analytics para uma conta de armazenamento Azure ou centro de eventos, utilize a funcionalidade de exportação de dados do log Analytics do Monitor Azure. Ver [Log Analytics exportação de dados do espaço de trabalho no Azure Monitor (pré-visualização)](./logs-data-export.md)
- Exportação programada de uma consulta de log utilizando uma App Lógica. Isto é semelhante ao recurso de exportação de dados, mas permite-lhe enviar dados filtrados ou agregados para o armazenamento do Azure. Este método, no entanto, está sujeito a [limites](../service-limits.md#log-analytics-workspaces)  de consulta de log Ver [dados do Arquivo do Log Analytics para o armazenamento Azure usando a Logic App](./logs-export-logic-app.md).
- Uma vez exportar usando uma App Lógica. Consulte [o conector de registos Azure Monitor para aplicações lógicas e automatização de energia](./logicapp-flow-connector.md).
- Uma vez exporta para máquina local usando o script PowerShell. Consulte [Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).

> [!TIP]
> Pode utilizar um cluster Azure Data Explorer existente ou criar um novo cluster dedicado com as configurações necessárias.

## <a name="create-an-external-table-located-in-azure-blob-storage"></a>Criar uma tabela externa localizada no armazenamento de bolhas Azure
Utilize [tabelas externas](/azure/data-explorer/kusto/query/schema-entities/externaltables) para ligar o Azure Data Explorer a uma conta de armazenamento Azure. Uma tabela externa é uma entidade de esquemas kusto que faz referência a dados armazenados fora de uma base de dados kusto. Como mesas, uma mesa externa tem um esquema bem definido. Ao contrário das tabelas, os dados são armazenados e geridos fora de um cluster Kusto. Os dados exportados da secção anterior são guardados nas linhas JSON.

Para criar uma referência, é necessário o esquema da tabela exportada. Utilize o operador [de getschema](/azure/data-explorer/kusto/query/getschemaoperator) do Log Analytics para obter esta informação que inclui as colunas da tabela e os seus tipos de dados.

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-map-schema.jpg" alt-text="Registar esquema de mesa de analítico.":::

Agora pode utilizar a saída para criar a consulta Kusto para a construção da tabela externa.
Seguindo as orientações em [Criar e alterar tabelas externas no Azure Storage ou no Azure Data Lake,](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake)crie uma tabela externa em formato JSON e, em seguida, execute a consulta a partir da sua base de dados Azure Data Explorer.

>[!NOTE]
>A criação de mesa externa é construída a partir de dois processos. A primeira é criar a tabela externa, enquanto a segunda é criar o mapeamento.

O seguinte script PowerShell criará os comandos [de criação](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake#create-external-table-mapping) para a tabela e o mapeamento.

```powershell
PARAM(
    $resourcegroupname, #The name of the Azure resource group
    $TableName, # The Log Analytics table you wish to convert to external table
    $MapName, # The name of the map
    $subscriptionId, #The ID of the subscription
    $WorkspaceId, # The Log Analytics WorkspaceId
    $WorkspaceName, # The Log Analytics workspace name
    $BlobURL, # The Blob URL where the data is saved
    $ContainerAccessKey, # The blob container Access Key (Option to add a SAS url)
    $ExternalTableName = $null # The External Table name, null to use the same name
)

if($null -eq $ExternalTableName) {
    $ExternalTableName = $TableName
}

$query = $TableName + ' | getschema | project ColumnName, DataType'

$output = (Invoke-AzOperationalInsightsQuery -WorkspaceId $WorkspaceId -Query $query).Results

$FirstCommand = @()
$SecondCommand = @()

foreach ($record in $output) {
    if ($record.DataType -eq 'System.DateTime') {
        $dataType = 'datetime'
    } else {
        $dataType = 'string'
    }
    $FirstCommand += $record.ColumnName + ":" + "$dataType" + ","
    $SecondCommand += "{`"column`":" + "`"" + $record.ColumnName + "`"," + "`"datatype`":`"$dataType`",`"path`":`"$." + $record.ColumnName + "`"},"
}
$schema = ($FirstCommand -join '') -replace ',$'
$mapping = ($SecondCommand -join '') -replace ',$'

$CreateExternal = @'
.create external table {0} ({1})
kind=blob
partition by (TimeGeneratedPartition:datetime = bin(TimeGenerated, 1min))
pathformat = (datetime_pattern("'y='yyyy'/m='MM'/d='dd'/h='HH'/m='mm", TimeGeneratedPartition))
dataformat=multijson
(
   h@'{2}/WorkspaceResourceId=/subscriptions/{4}/resourcegroups/{6}/providers/microsoft.operationalinsights/workspaces/{5};{3}'
)
with
(
   docstring = "Docs",
   folder = "ExternalTables"
)
'@ -f $TableName, $schema, $BlobURL, $ContainerAccessKey, $subscriptionId, $WorkspaceName, $resourcegroupname,$WorkspaceId

$createMapping = @'
.create external table {0} json mapping "{1}" '[{2}]'
'@ -f $ExternalTableName, $MapName, $mapping

Write-Host -ForegroundColor Red 'Copy and run the following commands (one by one), on your Azure Data Explorer cluster query window to create the external table and mappings:'
write-host -ForegroundColor Green $CreateExternal
Write-Host -ForegroundColor Green $createMapping
```

A imagem a seguir mostra e exemplo da saída.

:::image type="content" source="media/azure-data-explorer-query-storage/external-table-create-command-output.png" alt-text="ExternalTable criar saída de comando.":::

[![Saída de exemplo](media/azure-data-explorer-query-storage/external-table-create-command-output.png)](media/azure-data-explorer-query-storage/external-table-create-command-output.png#lightbox)

>[!TIP]
>* Copie, cole e, em seguida, execute a saída do script na sua ferramenta cliente Azure Data Explorer para criar a tabela e o mapeamento.
>* Para utilizar todos os dados dentro do recipiente, altere o script e altere o URL para https://your.blob.core.windows.net/containername ser ; SecKey'

## <a name="query-the-exported-data-from-azure-data-explorer"></a>Consulta dos dados exportados do Azure Data Explorer 

Depois de configurar o mapeamento, pode consultar os dados exportados do Azure Data Explorer. A sua consulta requer a função [external_table,](/azure/data-explorer/kusto/query/externaltablefunction) como no exemplo seguinte.

```kusto
external_table("HBTest","map") | take 10000
```

[![Consulta Log Analytics dados exportados](media/azure-data-explorer-query-storage/external-table-query.png)](media/azure-data-explorer-query-storage/external-table-query.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

- Aprenda a [escrever consultas no Azure Data Explorer](/azure/data-explorer/write-queries)