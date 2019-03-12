---
title: Por meio de programação monitorizar uma fábrica de dados do Azure | Documentos da Microsoft
description: Saiba como monitorizar um pipeline na fábrica de dados com os kits de desenvolvimento de software diferente (SDKs).
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: ed1d6ab96a7658880a8784c5e03c3787cf87a8ba
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576315"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Por meio de programação monitorizar uma fábrica de dados do Azure
Este artigo descreve como monitorizar um pipeline na fábrica de dados com os kits de desenvolvimento de software diferente (SDKs). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>Intervalo de dados

Fábrica de dados só armazena a execução do pipeline dados para 45 dias. Quando consulta por meio de programação para dados sobre execuções de pipeline do Data Factory - por exemplo, com o comando do PowerShell `Get-AzDataFactoryV2PipelineRun` -não existem nenhum datas máximas para o opcional `LastUpdatedAfter` e `LastUpdatedBefore` parâmetros. Mas se consultar os dados do ano passado, por exemplo, a consulta não devolveu um erro, mas só devolve pipeline de execução de dados dos últimos 45 dias.

Se pretender manter os dados por mais de 45 dias de execução do pipeline, configurar o seu próprio log de diagnóstico com [do Azure Monitor](monitor-using-azure-monitor.md).

## <a name="net"></a>.NET
Para obter instruções completas de criar e monitorizar um pipeline com o SDK de .NET, consulte [criar uma fábrica de dados e um pipeline com o .NET](quickstart-create-data-factory-dot-net.md).

1. Adicione o seguinte código para verificar continuamente o estado do pipeline executar até terminar de copiar os dados.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Adicione o seguinte código para esse obtém cópia da execução da atividade obter detalhes, por exemplo, o tamanho dos dados lidos/escritos.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

Para obter documentação completa sobre o SDK de .NET, consulte [referência SDK .NET do Data Factory](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet).

## <a name="python"></a>Python
Para obter instruções completas de criar e monitorizar um pipeline com o SDK de Python, veja [criar uma fábrica de dados e um pipeline com o Python](quickstart-create-data-factory-python.md).

Para monitorizar a execução de pipeline, adicione o seguinte código:

```python
#Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

Para obter documentação completa sobre o SDK de Python, veja [referência SDK de Python de fábrica de dados](/python/api/overview/azure/datafactory?view=azure-python).

## <a name="rest-api"></a>API REST
Para obter instruções completas de criar e monitorizar um pipeline com a REST API, consulte [criar uma fábrica de dados e um pipeline com a REST API](quickstart-create-data-factory-rest-api.md).
 
1. Execute o script seguinte para verificar continuamente o estado de execução do pipeline até que este termine de copiar os dados.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ($response.Status -eq "InProgress") {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```
2. Execute o script seguinte para obter os detalhes da execução da atividade de cópia, como, por exemplo, o tamanho dos dados lidos/escritos.

    ```PowerShell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

Para obter documentação completa sobre a REST API, consulte [referência de API de REST do Data Factory](/rest/api/datafactory/).

## <a name="powershell"></a>PowerShell
Para obter instruções completas de criar e monitorizar um pipeline com o PowerShell, consulte [criar uma fábrica de dados e um pipeline com o PowerShell](quickstart-create-data-factory-powershell.md).

1. Execute o script seguinte para verificar continuamente o estado de execução do pipeline até que este termine de copiar os dados.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 30
    }
    ```
2. Execute o script seguinte para obter os detalhes da execução da atividade de cópia, como, por exemplo, o tamanho dos dados lidos/escritos.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

Para obter documentação completa sobre os cmdlets do PowerShell, consulte [referência de cmdlets do PowerShell da fábrica de dados](/powershell/module/az.datafactory).

## <a name="next-steps"></a>Passos Seguintes
Ver [monitorizar pipelines com o Azure Monitor](monitor-using-azure-monitor.md) artigo para saber como utilizar o Azure Monitor para monitorizar os pipelines do Data Factory. 

