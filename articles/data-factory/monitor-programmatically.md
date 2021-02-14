---
title: Monitorize programáticamente uma fábrica de dados Azure
description: Aprenda a monitorizar um oleoduto numa fábrica de dados utilizando diferentes kits de desenvolvimento de software (SDKs).
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/16/2018
author: dcstwh
ms.author: weetok
ms.custom: devx-track-python
ms.openlocfilehash: 038da033c2bdf78a0a2547cc713944bc11bf093d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379901"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Monitorize programáticamente uma fábrica de dados Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como monitorizar um oleoduto numa fábrica de dados utilizando diferentes kits de desenvolvimento de software (SDKs). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>Gama de dados

A Data Factory só armazena dados de funcionação do gasoduto durante 45 dias. Quando consulta programáticamente dados sobre o gasoduto Data Factory - por exemplo, com o comando PowerShell `Get-AzDataFactoryV2PipelineRun` - não existem datas máximas para o opcional `LastUpdatedAfter` e `LastUpdatedBefore` parâmetros. Mas se consultar dados do ano passado, por exemplo, não terá um erro, mas apenas os dados de funcionação do pipeline dos últimos 45 dias.

Se pretender manter os dados de funcionamento do gasoduto durante mais de 45 dias, confiem o seu próprio registo de diagnóstico com [o Azure Monitor](monitor-using-azure-monitor.md).

## <a name="pipeline-run-information"></a>Informação de execução do gasoduto

Para obter propriedades de execução de gasodutos, consulte a [referência PipelineRun API](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/get#pipelinerun). Uma corrida de gasodutos tem um estatuto diferente durante o seu ciclo de vida, os valores possíveis do estado de funcionação são listados abaixo:

* Em fila
* InProgress
* Com êxito
* Com falhas
* A Cancelar
* Cancelado

## <a name="net"></a>.NET
Para obter um walk-through completo da criação e monitorização de um gasoduto utilizando .NET SDK, consulte [Criar uma fábrica de dados e um gasoduto utilizando .NET](quickstart-create-data-factory-dot-net.md).

1. Adicione o seguinte código para verificar continuamente o estado do gasoduto até terminar de copiar os dados.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress" || pipelineRun.Status == "Queued")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Adicione o seguinte código ao que recupera a atividade da cópia, por exemplo, o tamanho dos dados lidos/escritos.

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

Para obter documentação completa em .NET SDK, consulte a [referência Data Factory .NET SDK](/dotnet/api/microsoft.azure.management.datafactory).

## <a name="python"></a>Python
Para obter uma completa passagem pela criação e monitorização de um gasoduto utilizando a Python SDK, consulte [Criar uma fábrica de dados e um oleoduto utilizando python](quickstart-create-data-factory-python.md).

Para monitorizar o funcionado do gasoduto, adicione o seguinte código:

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

Para obter documentação completa sobre Python SDK, consulte a [referência SDK python da fábrica de dados](/python/api/overview/azure/datafactory).

## <a name="rest-api"></a>API REST
Para obter uma completa passagem pela criação e monitorização de um gasoduto utilizando a API REST, consulte [Criar uma fábrica de dados e um oleoduto utilizando a API REST](quickstart-create-data-factory-rest-api.md).
 
1. Execute o script seguinte para verificar continuamente o estado de execução do pipeline até que este termine de copiar os dados.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ( ($response.Status -eq "InProgress") -or ($response.Status -eq "Queued") ) {
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

Para obter documentação completa sobre a API REST, consulte [a referência API do Rest da Fábrica de Dados.](/rest/api/datafactory/)

## <a name="powershell"></a>PowerShell
Para obter uma utilização completa da criação e monitorização de um gasoduto utilizando o PowerShell, consulte [criar uma fábrica de dados e um oleoduto utilizando o PowerShell](quickstart-create-data-factory-powershell.md).

1. Execute o script seguinte para verificar continuamente o estado de execução do pipeline até que este termine de copiar os dados.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ( ($run.Status -ne "InProgress") -and ($run.Status -ne "Queued") ) {
                Write-Output ("Pipeline run finished. The status is: " +  $run.Status)
                $run
                break
            }
            Write-Output ("Pipeline is running...status: " + $run.Status)
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

Para obter documentação completa sobre cmdlets PowerShell, consulte [a referência cmdlet powershell da data factory](/powershell/module/az.datafactory).

## <a name="next-steps"></a>Passos seguintes
Consulte os gasodutos Monitor utilizando o artigo [do Azure Monitor](monitor-using-azure-monitor.md) para saber se utiliza o Monitor Azure para monitorizar os oleodutos da Data Factory. 

