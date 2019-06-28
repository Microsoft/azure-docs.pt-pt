---
title: Limpar a tarefa do Azure Stream Analytics
description: Este artigo mostra-lhe diferentes métodos para eliminar as tarefas de Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 6/21/2019
ms.custom: seodec18
ms.openlocfilehash: cb81c73f7946a10bae0470a55dcf1c0d55c2b847
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330039"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Parar ou eliminar a tarefa do Azure Stream Analytics

Tarefas do Azure Stream Analytics podem ser facilmente paradas ou eliminadas através do portal do Azure, Azure PowerShell, Azure SDK para .net ou a REST API. Não é possível recuperar uma tarefa do Stream Analytics, uma vez que foi eliminado.

>[!NOTE] 
>Quando parar a tarefa de Stream Analytics, os dados presentes apenas no armazenamento de entrada e saído, como os Hubs de eventos ou a base de dados do Azure SQL. Se forem necessárias para remover os dados do Azure, certifique-se de que siga o processo de remoção para os recursos de entrada e saídos da sua tarefa do Stream Analytics.

## <a name="stop-a-job-in-azure-portal"></a>Parar uma tarefa no portal do Azure

Quando parar uma tarefa, os recursos são deprovisionned e deixa de processamento de eventos. Custos relacionados com esta tarefa também são parados. No entanto, toda a sua configuração são mantidas e pode reiniciar a tarefa mais tarde 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

2. Localize a tarefa de Stream Analytics em execução e selecioná-lo.

3. Na página de tarefa do Stream Analytics, selecione **parar** para parar a tarefa. 

   ![Parar a tarefa do Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Eliminar uma tarefa no portal do Azure

>[!WARNING] 
>Não é possível recuperar uma tarefa do Stream Analytics, uma vez que foi eliminado.

1. Inicie sessão no Portal do Azure. 

2. Localize a tarefa de Stream Analytics existente e selecioná-lo.

3. Na página de tarefa do Stream Analytics, selecione **eliminar** para eliminar a tarefa. 

   ![Eliminar tarefa do Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Parar ou eliminar uma tarefa com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para parar uma tarefa com o PowerShell, utilize o [Stop-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) cmdlet. Para eliminar uma tarefa com o PowerShell, utilize o [Remove-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) cmdlet.

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Parar ou eliminar uma tarefa com o Azure SDK para .NET

Para parar uma tarefa com o Azure SDK para .NET, utilize o [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) método. Para eliminar uma tarefa com o Azure SDK para .NET, [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) método.

## <a name="stop-or-delete-a-job-using-rest-api"></a>Parar ou eliminar uma tarefa com a REST API

Para parar uma tarefa com a REST API, consulte a [parar](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop) método. Para eliminar uma tarefa com a REST API, consulte a [eliminar](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete) método.
