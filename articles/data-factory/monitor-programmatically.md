---
title: Monitorize programáticamente uma fábrica de dados azure
description: Aprenda a monitorizar um oleoduto numa fábrica de dados utilizando diferentes kits de desenvolvimento de software (SDKs).
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: d416a4a2bace2aeced6961d4959b0478feb0e650
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81398808"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Monitorize programáticamente uma fábrica de dados azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como monitorizar um oleoduto numa fábrica de dados utilizando diferentes kits de desenvolvimento de software (SDKs). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>Intervalo de dados

A Fábrica de Dados só armazena dados de gasodutos durante 45 dias. Quando se consulta programaticamente por dados sobre o pipeline data Factory `Get-AzDataFactoryV2PipelineRun` - por exemplo, com `LastUpdatedAfter` o `LastUpdatedBefore` comando PowerShell - não existem datas máximas para os parâmetros opcionais e parâmetros. Mas se consultar dados relativos ao ano passado, por exemplo, a consulta não devolve um erro, mas apenas devolve dados de execução de gasodutos dos últimos 45 dias.

Se pretender persistir os dados de execução do gasoduto por mais de 45 dias, instale o seu próprio registo de diagnóstico com o [Monitor Azure](monitor-using-azure-monitor.md).

## <a name="net"></a>.NET
Para uma passagem completa de criação e monitorização de um gasoduto utilizando o .NET SDK, consulte [Criar uma fábrica de dados e um gasoduto utilizando .NET](quickstart-create-data-factory-dot-net.md).

1. Adicione o seguinte código para verificar continuamente o estado do gasoduto até que termine de copiar os dados.

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

2. Adicione o seguinte código a que recupera os detalhes de execução da atividade da cópia, por exemplo, o tamanho dos dados lidos/escritos.

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

Para obter documentação completa em .NET SDK, consulte [data factory .NET SDK referência](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet).

## <a name="python"></a>Python
Para uma completa passagem pela criação e monitorização de um oleoduto utilizando o Python SDK, consulte [Criar uma fábrica de dados e um oleoduto utilizando python](quickstart-create-data-factory-python.md).

Para monitorizar o gasoduto, adicione o seguinte código:

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

Para obter documentação completa sobre python SDK, consulte [data factory Python SDK referência](/python/api/overview/azure/datafactory?view=azure-python).

## <a name="rest-api"></a>API REST
Para uma passagem completa pela criação e monitorização de um gasoduto utilizando a API REST, consulte [Criar uma fábrica de dados e um gasoduto utilizando a Rest API](quickstart-create-data-factory-rest-api.md).
 
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

Para obter documentação completa sobre a API REST, consulte data [factory REST API referência](/rest/api/datafactory/).

## <a name="powershell"></a>PowerShell
Para uma passagem completa pela criação e monitorização de um oleoduto utilizando o PowerShell, consulte Criar uma fábrica de dados e um oleoduto utilizando o [PowerShell](quickstart-create-data-factory-powershell.md).

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

Para obter documentação completa sobre os cmdlets PowerShell, consulte a [referência de cmdlet da Fábrica](/powershell/module/az.datafactory)de Dados PowerShell .

## <a name="next-steps"></a>Passos seguintes
Consulte [os gasodutos Monitor utilizando](monitor-using-azure-monitor.md) o artigo do Azure Monitor para aprender sobre a utilização do Monitor Azure para monitorizar os gasodutos data Factory. 

