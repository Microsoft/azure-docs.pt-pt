---
title: Como iniciar uma tarefa do Azure Stream Analytics
description: Este artigo descreve como iniciar uma tarefa do Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 9bc3e4132919e5fc5baadc78841e66efd3c34bcd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61362270"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Como iniciar uma tarefa do Azure Stream Analytics

Pode iniciar seu trabalho do Azure Stream Analytics com o portal do Azure, o Visual Studio e o PowerShell. Quando iniciar uma tarefa, selecione uma hora para a tarefa iniciar a criação da saída. Portal do Azure, Visual Studio e PowerShell têm diferentes métodos para definir a hora de início. Esses métodos são descritos abaixo.

## <a name="start-options"></a>Opções de início
As três opções seguintes estão disponíveis para iniciar uma tarefa. Tenha em atenção que todas as vezes mencionadas a seguir são as especificadas [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics). Se TIMESTAMP BY não for especificada, será utilizado o tempo de chegada.
* **Agora**: Faz com que o ponto de partida do evento de saída igual do stream quando a tarefa é iniciada. Se for utilizado um operador temporal (por exemplo, janela de tempo, ATRASO ou uma associação), Azure Stream Analytics será automaticamente olhar novamente os dados na origem de entrada. Por exemplo, se iniciar uma tarefa de "Agora" e a consulta utilizar uma janela em cascata de 5 minutos, o Azure Stream Analytics vai procurar dados a partir de 5 minutos atrás na entrada.
O primeiro evento de saída possível teria um carimbo igual ou superior à hora atual e ASA garante que todos os eventos de entrada que logicamente podem contribuir para a saída tem foram consideradas. Por exemplo, não agregados em janelas parciais são gerados. É sempre o valor agregado completado.

* **Custom**: Pode escolher o ponto de partida da saída. Da mesma forma para o **agora** opção, o Azure Stream Analytics lê automaticamente os dados antes desta vez, se for utilizado um operador temporal 

* **Quando foi a última paragem**. Esta opção está disponível quando a tarefa foi iniciada anteriormente, mas foi parada manualmente ou falha. Ao escolher esta opção do Azure Stream Analytics irá utilizar a última hora de saída para reiniciar a tarefa para que nenhum dado seja perdido. Da mesma forma para opções anteriores, Azure Stream Analytics lê automaticamente os dados antes desta vez se for utilizado um operador temporal. Uma vez que várias partições de entrada podem ter a hora diferente, é utilizada a primeira hora de paragem de todas as partições, como resultado de alguns duplicados podem ser vistos na saída. Obter mais informações sobre exatamente-processamento uma vez estão disponíveis na página [garantias de entrega de eventos](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).


## <a name="azure-portal"></a>Portal do Azure

Navegue para a sua tarefa no portal do Azure e selecione **iniciar** na página de descrição geral. Selecione um **hora de início da saída da tarefa** e, em seguida, selecione **iniciar**.

Escolha uma das opções para **hora de início da saída da tarefa**. As opções são *agora*, *personalizada*, e, se a tarefa foi executada anteriormente, *quando foi a última paragem*. Veja acima para obter mais informações sobre estas opções.

## <a name="visual-studio"></a>Visual Studio

Na vista de tarefas, selecione o botão de seta verde para iniciar a tarefa. Definir o **modo de início de saída na tarefa** e selecione **iniciar**. O estado da tarefa será alterado para **em execução**.

Existem três opções para **iniciar modo de saída tarefa**: *JobStartTime*, *CustomTime*, e *LastOutputEventTime*. Se esta propriedade estiver ausente, a predefinição é *JobStartTime*. Veja acima para obter mais informações sobre estas opções.


## <a name="powershell"></a>PowerShell

Utilize o cmdlet seguinte para iniciar seu trabalho com o PowerShell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Existem três opções para **OutputStartMode**: *JobStartTime*, *CustomTime*, e *LastOutputEventTime*. Se esta propriedade estiver ausente, a predefinição é *JobStartTime*. Veja acima para obter mais informações sobre estas opções.

Para obter mais informações sobre o `Start-AzStreamAnalyitcsJob` cmdlet, ver o [início AzStreamAnalyticsJob referência](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Passos Seguintes

* [Quickstart: Criar uma tarefa de Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)
* [Quickstart: Criar uma tarefa do Stream Analytics com o Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Quickstart: Criar uma tarefa de Stream Analytics, utilizando as ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-quick-create-vs.md)
