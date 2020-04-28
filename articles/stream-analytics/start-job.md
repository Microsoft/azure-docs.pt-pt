---
title: Como iniciar um trabalho de Azure Stream Analytics
description: Este artigo descreve como iniciar um trabalho de Stream Analytics a partir do portal Azure, PowerShell e Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: c393eb782c2ff16eb5b3e5967b39938dfe2f1534
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75426472"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Como iniciar um trabalho de Azure Stream Analytics

Pode iniciar o seu trabalho de Azure Stream Analytics utilizando o portal Azure, Visual Studio e PowerShell. Quando se começa a trabalhar, seleciona-se um momento para o trabalho começar a criar saída. O portal Azure, o Visual Studio e o PowerShell têm métodos diferentes para definir a hora de início. Estes métodos são descritos abaixo.

## <a name="start-options"></a>Opções de início
As três seguintes opções estão disponíveis para iniciar um trabalho. Note que todos os tempos abaixo mencionados são os especificados no [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics). Se o TIMESTAMP BY não for especificado, será utilizada a hora de chegada.
* **Agora**: Faz com que o ponto de partida do evento de saída seja igual ao de quando o trabalho é iniciado. Se for utilizado um operador temporal (por exemplo, janela de tempo, LAG ou JOIN), o Azure Stream Analytics olhará automaticamente para os dados da fonte de entrada. Por exemplo, se iniciar um trabalho "Now" e se a sua consulta utilizar uma Janela Tumbling de 5 minutos, o Azure Stream Analytics procurará dados de há 5 minutos na entrada.
O primeiro evento de saída possível teria um carimbo de tempo igual ou superior ao tempo atual, e a ASA garante que todos os eventos de entrada que possam contribuir logicamente para a produção foram contabilizados. Por exemplo, não são gerados agregados parciais com janelas. É sempre o valor agregado completo.

* **Personalizado**: Pode escolher o ponto de partida da saída. Da mesma forma que a opção **Now,** o Azure Stream Analytics irá ler automaticamente os dados antes desta altura se um operador temporal for utilizado 

* **Da última vez que parou.** Esta opção está disponível quando o trabalho foi previamente iniciado, mas foi interrompido manualmente ou falhado. Ao escolher esta opção, o Azure Stream Analytics utilizará o último tempo de saída para reiniciar o trabalho para que não se percam dados. Da mesma forma que as opções anteriores, o Azure Stream Analytics irá ler automaticamente os dados antes desta altura se um operador temporal for utilizado. Uma vez que várias divisórias de entrada podem ter tempo sinuoso, o tempo de paragem mais antigo de todas as divisórias é usado, como resultado, alguns duplicatos podem ser vistos na saída. Mais informações sobre o processamento exata mente uma vez estão disponíveis na página Garantias de Entrega de [Eventos](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).


## <a name="azure-portal"></a>Portal do Azure

Navegue para o seu trabalho no portal Azure e selecione **Iniciar** na página de visão geral. Selecione um tempo de **arranque de saída de trabalho** e, em seguida, selecione **Iniciar**.

Escolha uma das opções para o início da saída do **Trabalho**. As opções são *Agora,* *Personalizadas,* e, se o trabalho foi anteriormente executado, *Quando pela última vez parou*. Veja acima mais informações sobre estas opções.

## <a name="visual-studio"></a>Visual Studio

Na vista do trabalho, selecione o botão de seta verde para iniciar o trabalho. Desajuste o modo de arranque da saída de **trabalho** e selecione **Start**. O estado do trabalho mudará para **Running**.

Existem três opções para o modo de **arranque da saída de trabalho**: *JobStartTime,* *CustomTime*, e *LastOutputEventTime*. Se esta propriedade estiver ausente, o padrão é *JobStartTime*. Veja acima mais informações sobre estas opções.


## <a name="powershell"></a>PowerShell

Utilize o seguinte cmdlet para iniciar o seu trabalho utilizando a PowerShell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Existem três opções para **OutputStartMode**: *JobStartTime,* *CustomTime*, e *LastOutputEventTime*. Se esta propriedade estiver ausente, o padrão é *JobStartTime*. Veja acima mais informações sobre estas opções.

Para obter mais `Start-AzStreamAnalyitcsJob` informações sobre o cmdlet, consulte a [referência Start-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Passos seguintes

* [Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)
* [Quickstart: Criar um trabalho de Stream Analytics usando o Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Início Rápido: Criar uma tarefa do Stream Analytics com as ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-quick-create-vs.md)
