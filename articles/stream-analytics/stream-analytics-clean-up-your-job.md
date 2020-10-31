---
title: Limpe o seu trabalho Azure Stream Analytics
description: Este artigo mostra-lhe diferentes métodos para apagar os seus trabalhos Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 124d2cf4380153bd3ea68de9233f6a1b5eb6ba78
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130702"
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

1. Inicie sessão no portal do Azure. 

2. Localize o seu trabalho stream analytics existente e selecione-o.

3. Na página de trabalho stream Analytics, **selecione Eliminar** para eliminar o trabalho. 

   ![Excluir trabalho de análise de fluxo de Azure](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Parar ou apagar um trabalho usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para impedir um trabalho utilizando o PowerShell, utilize o [cmdlet Stop-AzStreamAnalyticsJob.](/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) Para eliminar um trabalho utilizando o PowerShell, utilize o [cmdlet Remove-AzStreamAnalyticsJob.](/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob)

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Parar ou apagar um trabalho utilizando a Azure SDK para .NET

Para impedir uma tarefa utilizando o método Azure SDK para .NET, utilize o [método StreamingJobsOperationsExtensions.BeginStop.](/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) Para eliminar um trabalho utilizando o método Azure SDK para .NET, [StreamingJobsOperationsExtensions.BeginDelete.](/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet)

## <a name="stop-or-delete-a-job-using-rest-api"></a>Parar ou excluir um trabalho usando REST API

Para impedir um trabalho utilizando a API REST, consulte o método [Stop.](/powershell/module/azurerm.streamanalytics/stop-azurermstreamanalyticsjob#stop) Para eliminar um trabalho utilizando a API REST, consulte o método [Eliminar.](/powershell/module/azurerm.streamanalytics/stop-azurermstreamanalyticsjob#delete)