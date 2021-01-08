---
title: Limpe o seu trabalho Azure Stream Analytics
description: Este artigo mostra-lhe diferentes métodos para apagar os seus trabalhos Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 31812ac805bd3465b1b735842b45adb372286d66
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016070"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Pare ou elimine o seu trabalho Azure Stream Analytics

Os trabalhos do Azure Stream Analytics podem ser facilmente interrompidos ou eliminados através do portal Azure PowerShell, Azure SDK para .Net ou REST API. Um trabalho stream Analytics não pode ser recuperado uma vez que tenha sido eliminado.

>[!NOTE] 
>Quando para o seu trabalho stream Analytics, os dados persistem apenas no armazenamento de entradas e saídas, tais como Event Hubs ou Azure SQL Database. Se for obrigado a remover dados do Azure, certifique-se de seguir o processo de remoção para os recursos de entrada e saída do seu trabalho stream Analytics.

## <a name="stop-a-job-in-azure-portal"></a>Parar um emprego no portal Azure

Quando se para um emprego, os recursos são desprovisionados e para de processar eventos. As acusações relacionadas com este trabalho também estão paradas. No entanto, toda a sua configuração é mantida e pode reiniciar o trabalho mais tarde 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

2. Localize o seu trabalho de Stream Analytics em execução e selecione-o.

3. Na página de trabalho stream Analytics, **selecione Stop** para parar o trabalho. 

   ![Parar o trabalho de Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Excluir um trabalho no portal Azure

>[!WARNING] 
>Um trabalho stream Analytics não pode ser recuperado uma vez que tenha sido eliminado.

1. Inicie sessão no Portal do Azure. 

2. Localize o seu trabalho stream analytics existente e selecione-o.

3. Na página de trabalho stream Analytics, **selecione Eliminar** para eliminar o trabalho. 

   ![Excluir trabalho de análise de fluxo de Azure](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Parar ou apagar um trabalho usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para impedir um trabalho utilizando o PowerShell, utilize o [cmdlet Stop-AzStreamAnalyticsJob.](/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) Para eliminar um trabalho utilizando o PowerShell, utilize o [cmdlet Remove-AzStreamAnalyticsJob.](/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob)

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Parar ou apagar um trabalho utilizando a Azure SDK para .NET

Para impedir uma tarefa utilizando o método Azure SDK para .NET, utilize o [método StreamingJobsOperationsExtensions.BeginStop.](/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop) Para eliminar um trabalho utilizando o método Azure SDK para .NET, [StreamingJobsOperationsExtensions.BeginDelete.](/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete)

## <a name="stop-or-delete-a-job-using-rest-api"></a>Parar ou excluir um trabalho usando REST API

Para impedir um trabalho utilizando a API REST, consulte o método [Stop.](/rest/api/streamanalytics/2016-03-01/streamingjobs/stop) Para eliminar um trabalho utilizando a API REST, consulte o método [Eliminar.](/rest/api/streamanalytics/2016-03-01/streamingjobs/delete)