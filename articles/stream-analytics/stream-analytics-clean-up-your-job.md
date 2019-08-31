---
title: Limpar a tarefa do Azure Stream Analytics
description: Este artigo mostra-lhe diferentes métodos para eliminar as tarefas de Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 093ada2b2751540b986154be3a2f333784c1259f
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173293"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Parar ou excluir seu trabalho de Azure Stream Analytics

Azure Stream Analytics trabalhos podem ser facilmente interrompidos ou excluídos por meio do portal do Azure, Azure PowerShell, SDK do Azure para .net ou API REST. Um trabalho de Stream Analytics não pode ser recuperado depois de ser excluído.

>[!NOTE] 
>Quando parar a tarefa de Stream Analytics, os dados presentes apenas no armazenamento de entrada e saído, como os Hubs de eventos ou a base de dados do Azure SQL. Se forem necessárias para remover os dados do Azure, certifique-se de que siga o processo de remoção para os recursos de entrada e saídos da sua tarefa do Stream Analytics.

## <a name="stop-a-job-in-azure-portal"></a>Parar uma tarefa no portal do Azure

Quando você interrompe um trabalho, os recursos são desprovisionados e o processamento de eventos é interrompido. Os encargos relacionados a esse trabalho também são interrompidos. No entanto, todas as suas configurações são mantidas e você pode reiniciar o trabalho mais tarde 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

2. Localize a tarefa de Stream Analytics em execução e selecioná-lo.

3. Na página de tarefa do Stream Analytics, selecione **parar** para parar a tarefa. 

   ![Parar a tarefa do Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Eliminar uma tarefa no portal do Azure

>[!WARNING] 
>Um trabalho de Stream Analytics não pode ser recuperado depois de ser excluído.

1. Inicie sessão no Portal do Azure. 

2. Localize a tarefa de Stream Analytics existente e selecioná-lo.

3. Na página de tarefa do Stream Analytics, selecione **eliminar** para eliminar a tarefa. 

   ![Eliminar tarefa do Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Parar ou eliminar uma tarefa com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para interromper um trabalho usando o PowerShell, use o cmdlet [Stop-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) . Para excluir um trabalho usando o PowerShell, use o cmdlet [Remove-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) .

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Parar ou eliminar uma tarefa com o Azure SDK para .NET

Para parar uma tarefa com o Azure SDK para .NET, utilize o [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) método. Para eliminar uma tarefa com o Azure SDK para .NET, [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) método.

## <a name="stop-or-delete-a-job-using-rest-api"></a>Parar ou eliminar uma tarefa com a REST API

Para parar uma tarefa com a REST API, consulte a [parar](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop) método. Para eliminar uma tarefa com a REST API, consulte a [eliminar](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete) método.
