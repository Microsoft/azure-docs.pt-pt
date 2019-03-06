---
title: Como iniciar uma tarefa do Azure Stream Analytics
description: Este artigo descreve como iniciar uma tarefa do Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2019
ms.openlocfilehash: aa089ed53554ec697bd9430cc95a7cce78aabed2
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57411530"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Como iniciar uma tarefa do Azure Stream Analytics

Pode iniciar seu trabalho do Azure Stream Analytics com o portal do Azure, o Visual Studio e o PowerShell. Quando iniciar uma tarefa, selecione uma hora para a tarefa iniciar a criação da saída. Portal do Azure, Visual Studio e PowerShell têm diferentes métodos para definir a hora de início. Esses métodos são descritos abaixo.

## <a name="azure-portal"></a>Portal do Azure

Navegue para a sua tarefa no portal do Azure e selecione **iniciar** na página de descrição geral. Selecione um **hora de início da saída da tarefa** e, em seguida, selecione **iniciar**.

Existem três opções para **hora de início da saída da tarefa**: *Agora*, *personalizado*, e *quando foi a última paragem*. Selecionando *agora* inicia a tarefa neste momento. Selecionando *personalizado* permite-lhe definir uma hora personalizada no passado ou o futuro da tarefa começar. Para retomar uma tarefa parada sem perder dados, escolha. Quando foi a última paragem *.

## <a name="visual-studio"></a>Visual Studio

Na vista de tarefas, selecione o botão de seta verde para iniciar a tarefa. Definir o **modo de início de saída na tarefa** e selecione **iniciar**. O estado da tarefa será alterado para **em execução**.

Existem três opções para **iniciar modo de saída tarefa**: *JobStartTime*, *CustomTime*, e *LastOutputEventTime*. Se esta propriedade estiver ausente, a predefinição é *JobStartTime*.

*JobStartTime* faz com que o ponto de partida do evento de saída transmitir o mesmo quando a tarefa for iniciada.

*CustomTime* começa a saída num tempo personalizado especificado no *OutputStartTime* parâmetro.

*LastOutputEventTime* faz com que o ponto de partida do fluxo de eventos de saída o mesmo que o último evento de saída do tempo.

## <a name="powershell"></a>PowerShell

Utilize o cmdlet seguinte para iniciar seu trabalho com o PowerShell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Existem três opções para **OutputStartMode**: *JobStartTime*, *CustomTime*, e *LastOutputEventTime*. Se esta propriedade estiver ausente, a predefinição é *JobStartTime*.

*JobStartTime* faz com que o ponto de partida do evento de saída transmitir o mesmo quando a tarefa for iniciada.

*CustomTime* começa a saída num tempo personalizado especificado no *OutputStartTime* parâmetro.

*LastOutputEventTime* faz com que o ponto de partida do fluxo de eventos de saída o mesmo que o último evento de saída do tempo.

Para obter mais informações sobre o `Start-AzStreamAnalyitcsJob` cmdlet, ver o [início AzStreamAnalyticsJob referência](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob.md).

## <a name="next-steps"></a>Passos Seguintes

* [Quickstart: Criar uma tarefa de Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)
* [Quickstart: Criar uma tarefa do Stream Analytics com o Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Quickstart: Criar uma tarefa de Stream Analytics, utilizando as ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-quick-create-vs.md)