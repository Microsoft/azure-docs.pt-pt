---
title: Como iniciar um trabalho do Azure Stream Analytics
description: Este artigo descreve como iniciar um trabalho stream Analytics a partir do portal Azure, PowerShell e Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/03/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bce1f00603d9304fc544fb92d4cc5f1a429504ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89069005"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Como iniciar um trabalho do Azure Stream Analytics

Pode iniciar o seu trabalho Azure Stream Analytics utilizando o portal Azure, Visual Studio e PowerShell. Quando se começa um trabalho, escolhe-se um tempo para o trabalho começar a criar output. Portal Azure, Visual Studio e PowerShell têm métodos diferentes para definir a hora de início. Estes métodos são descritos abaixo.

## <a name="start-options"></a>Opções de início
As três opções seguintes estão disponíveis para iniciar um trabalho. Note que todas as horas abaixo mencionadas são as especificadas no [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics). Se o TIMETAMP BY não for especificado, será utilizada a hora de chegada.
* **Agora**: Faz com que o ponto de partida do evento de saída flua da mesma forma que quando o trabalho é iniciado. Se for utilizado um operador temporal (por exemplo, janela de tempo, LAG ou JOIN), o Azure Stream Analytics olhará automaticamente para os dados na fonte de entrada. Por exemplo, se iniciar um trabalho "Agora" e se a sua consulta utilizar uma Janela Tumbling de 5 minutos, o Azure Stream Analytics procurará dados de há 5 minutos na entrada.
O primeiro evento de saída possível teria um calendário igual ou superior ao tempo atual, e a ASA garante que todos os eventos de entrada que possam contribuir logicamente para a saída foram contabilizados. Por exemplo, não são gerados agregados parciais com janelas. É sempre o valor agregado completo.

* **Costume**: Pode escolher o ponto de partida da saída. Da mesma forma que a opção **Now,** o Azure Stream Analytics irá ler automaticamente os dados antes deste tempo se um operador temporal for utilizado 

* **Quando a última vez parou.** Esta opção está disponível quando o trabalho foi iniciado anteriormente, mas foi interrompido manualmente ou falhou. Ao escolher esta opção, o Azure Stream Analytics utilizará o último tempo de saída para reiniciar o trabalho para que nenhum dado seja perdido. Da mesma forma que as opções anteriores, o Azure Stream Analytics irá ler automaticamente os dados antes deste tempo se um operador temporal for utilizado. Uma vez que várias divisórias de entrada podem ter um tempo diferente, o tempo de paragem mais antigo de todas as divisórias é usado, como resultado, alguns duplicados podem ser vistos na saída. Mais informações sobre o processamento exatamente uma vez estão disponíveis na página [Garantias de Entrega de Eventos.](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)


## <a name="azure-portal"></a>Portal do Azure

Navegue para o seu trabalho no portal Azure e selecione **Iniciar** na página de visão geral. Selecione uma **hora de início de saída de trabalho** e, em seguida, selecione **Iniciar**.

Escolha uma das opções para **a hora de início de saída do Trabalho**. As opções são *Agora*, *Personalizado*, e, se o trabalho foi previamente executado,  *Quando foi a última paragem*. Veja acima para mais informações sobre estas opções.

## <a name="visual-studio"></a>Visual Studio

Na vista de trabalho, selecione o botão de seta verde para iniciar o trabalho. Desa ajuste o **modo de início de saída de trabalho** e selecione **Iniciar**. O estatuto de trabalho mudará para **Running**.

Existem três opções para **modo de início de saída de trabalho**: *JobStartTime,* *CustomTime*e *LastOutputEventTime*. Se esta propriedade estiver ausente, o padrão é *JobStartTime*. Veja acima para mais informações sobre estas opções.


## <a name="powershell"></a>PowerShell

Utilize o seguinte cmdlet para iniciar o seu trabalho utilizando o PowerShell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Existem três opções para **OutputStartMode**: *JobStartTime,* *CustomTime*e *LastOutputEventTime*. Se esta propriedade estiver ausente, o padrão é *JobStartTime*. Veja acima para mais informações sobre estas opções.

Para obter mais informações sobre o `Start-AzStreamAnalyitcsJob` cmdlet, consulte a [referência Start-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Passos seguintes

* [Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)
* [Quickstart: Criar um trabalho stream analytics usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Início Rápido: Criar uma tarefa do Stream Analytics com as ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-quick-create-vs.md)
