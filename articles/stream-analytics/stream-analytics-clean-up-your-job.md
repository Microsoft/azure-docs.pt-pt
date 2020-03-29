---
title: Limpe o seu trabalho de Análise de Fluxo Sino
description: Este artigo mostra-lhe diferentes métodos para apagar os seus trabalhos de Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d99920417f20034da1001a821c02376ac19274d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426476"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Pare ou elimine o seu trabalho de Análise de Fluxo saqueado

Os trabalhos da Azure Stream Analytics podem ser facilmente interrompidos ou eliminados através do portal Azure, Azure PowerShell, Azure SDK para .Net ou REST API. Um trabalho de Stream Analytics não pode ser recuperado uma vez que tenha sido eliminado.

>[!NOTE] 
>Quando para o seu trabalho no Stream Analytics, os dados persistem apenas no armazenamento de entrada e saída, como hubs de eventos ou base de dados Azure SQL. Se for obrigado a remover dados do Azure, certifique-se de que segue o processo de remoção para os recursos de entrada e saída do seu trabalho stream analytics.

## <a name="stop-a-job-in-azure-portal"></a>Pare um trabalho no portal Azure

Quando se para um emprego, os recursos são desprovisionados e isso deixa de processar eventos. As acusações relacionadas com este trabalho também são interrompidas. No entanto, toda a sua configuração é mantida e pode reiniciar o trabalho mais tarde 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 

2. Localize o seu trabalho de streaming analytics e selecione-o.

3. Na página de trabalho do Stream Analytics, selecione **Stop** para parar o trabalho. 

   ![Trabalho de Stop Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Apague um trabalho no portal Azure

>[!WARNING] 
>Um trabalho de Stream Analytics não pode ser recuperado uma vez que tenha sido eliminado.

1. Inicie sessão no Portal do Azure. 

2. Localize o seu trabalho existente no Stream Analytics e selecione-o.

3. Na página de trabalho do Stream Analytics, selecione **Eliminar** para eliminar o trabalho. 

   ![Eliminar o trabalho de análise de fluxo de azure](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Pare ou elimine um trabalho usando powerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para parar um trabalho utilizando o PowerShell, utilize o cmdlet [Stop-AzStreamAnalyticsJob.](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) Para eliminar uma obra utilizando o PowerShell, utilize o cmdlet [Remove-AzStreamAnalyticsJob.](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob)

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Pare ou apague um trabalho utilizando O SDK Azure para .NET

Para parar um trabalho utilizando o Azure SDK para .NET, utilize o método [StreamingJobsOperationsExtensions.BeginStop.](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) Para eliminar um trabalho utilizando o Azure SDK para .NET, [streamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) método.

## <a name="stop-or-delete-a-job-using-rest-api"></a>Parar ou eliminar um trabalho utilizando a API REST

Para parar um trabalho utilizando a API REST, consulte o método [Stop.](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop) Para eliminar um trabalho utilizando a API REST, consulte o método [Eliminar.](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete)
