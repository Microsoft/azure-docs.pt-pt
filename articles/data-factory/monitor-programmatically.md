---
title: Monitorar programaticamente uma data factory do Azure
description: Saiba como monitorar um pipeline em um data factory usando SDKs (Software Development Kits) diferentes.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
author: djpmsft
ms.author: daperlov
manager: craigg
ms.openlocfilehash: f9e85a2660ffe1088a9897e9936b6fd0360f87d5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684611"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Monitorar programaticamente uma data factory do Azure
Este artigo descreve como monitorar um pipeline em um data factory usando SDKs (Software Development Kits) diferentes. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>Intervalo de dados

Data Factory só armazena dados de execução de pipeline por 45 dias. Quando você consulta programaticamente dados sobre execuções de pipeline Data Factory-por exemplo, com o comando do PowerShell `Get-AzDataFactoryV2PipelineRun`-não há nenhuma data máxima para os parâmetros opcionais `LastUpdatedAfter` e `LastUpdatedBefore`. Mas se você consultar dados para o ano passado, por exemplo, a consulta não retornará um erro, mas somente retornará dados de execução de pipeline dos últimos 45 dias.

Se você quiser manter os dados de execução do pipeline por mais de 45 dias, configure seu próprio log de diagnóstico com [Azure monitor](monitor-using-azure-monitor.md).

## <a name="net"></a>.NET
Para obter uma explicação completa de como criar e monitorar um pipeline usando o SDK do .NET, consulte [criar um data Factory e um pipeline usando o .net](quickstart-create-data-factory-dot-net.md).

1. Adicione o código a seguir para verificar continuamente o status da execução do pipeline até que ele termine de copiar os dados.

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

2. Adicione o código a seguir a que recupera os detalhes da execução da atividade de cópia, por exemplo, o tamanho dos dados lidos/gravados.

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

Para obter a documentação completa sobre o SDK do .NET, consulte [Data Factory referência do SDK do .net](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet).

## <a name="python"></a>Python
Para obter uma explicação completa de como criar e monitorar um pipeline usando o SDK do Python, consulte [criar um data Factory e um pipeline usando o Python](quickstart-create-data-factory-python.md).

Para monitorar a execução do pipeline, adicione o seguinte código:

```python
# Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(
    rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(
    rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

Para obter a documentação completa sobre o SDK do Python, consulte [Data Factory referência do SDK do Python](/python/api/overview/azure/datafactory?view=azure-python).

## <a name="rest-api"></a>API REST
Para obter uma explicação completa de como criar e monitorar um pipeline usando a API REST, consulte [criar um data Factory e um pipeline usando a API REST](quickstart-create-data-factory-rest-api.md).
 
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

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

Para obter a documentação completa sobre a API REST, consulte [Data Factory referência da API REST](/rest/api/datafactory/).

## <a name="powershell"></a>PowerShell
Para obter uma explicação completa de como criar e monitorar um pipeline usando o PowerShell, consulte [criar um data Factory e um pipeline usando o PowerShell](quickstart-create-data-factory-powershell.md).

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

Para obter a documentação completa sobre os cmdlets do PowerShell, consulte [Data Factory referência de cmdlet do PowerShell](/powershell/module/az.datafactory).

## <a name="next-steps"></a>Passos seguintes
Consulte [monitorar pipelines usando Azure monitor](monitor-using-azure-monitor.md) artigo para saber mais sobre como usar Azure monitor para monitorar data Factory pipelines. 

