---
title: Como iniciar um trabalho de Azure Stream Analytics
description: Este artigo descreve como iniciar um trabalho de Stream Analytics do portal do Azure, do PowerShell e do Visual Studio.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 1e4cb63accf7e89ac02451e9c25b9902a8a10812
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173281"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Como iniciar um trabalho de Azure Stream Analytics

Você pode iniciar seu trabalho de Azure Stream Analytics usando o portal do Azure, o Visual Studio e o PowerShell. Ao iniciar um trabalho, você seleciona uma hora para o trabalho começar a criar a saída. Portal do Azure, o Visual Studio e o PowerShell têm métodos diferentes para definir a hora de início. Esses métodos são descritos abaixo.

## <a name="start-options"></a>Opções de inicialização
As três opções a seguir estão disponíveis para iniciar um trabalho. Observe que todos os horários mencionados abaixo são os especificados no [carimbo de data/hora por](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics). Se TIMESTAMP não for especificado, a hora de chegada será usada.
* **Agora**: Faz com que o ponto inicial do evento de saída transmita o mesmo de quando o trabalho é iniciado. Se um operador temporal for usado (por exemplo, janela de tempo, retardo ou junção), Azure Stream Analytics examinará automaticamente os dados na fonte de entrada. Por exemplo, se você iniciar um trabalho "agora" e se sua consulta usar uma janela em cascata de 5 minutos, Azure Stream Analytics procurará dados de 5 minutos atrás na entrada.
O primeiro evento de saída possível teria um carimbo de data/hora igual ou maior do que a hora atual, e o ASA garante que todos os eventos de entrada que podem contribuir logicamente com a saída sejam contabilizados. Por exemplo, nenhuma agregação em janela parcial é gerada. É sempre o valor agregado completo.

* **Personalizado**: Você pode escolher o ponto de partida da saída. Da mesma forma, para a opção **Now** , Azure Stream Analytics lerá automaticamente os dados antes desta hora se um operador temporal for usado 

* **Quando a última parada**. Essa opção está disponível quando o trabalho foi iniciado anteriormente, mas foi interrompido manualmente ou falhou. Ao escolher essa opção Azure Stream Analytics usará a última hora de saída para reiniciar o trabalho, para que nenhum dado seja perdido. Da mesma forma que as opções anteriores, Azure Stream Analytics lerá automaticamente os dados antes dessa hora se um operador temporal for usado. Como várias partições de entrada podem ter um horário diferente, a hora de parada mais antiga de todas as partições é usada, como resultado, algumas duplicatas podem ser vistas na saída. Mais informações sobre o processamento exatamente uma vez estão disponíveis na página [garantias de entrega de eventos](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).


## <a name="azure-portal"></a>Portal do Azure

Navegue até o trabalho na portal do Azure e selecione **Iniciar** na página Visão geral. Selecione uma **hora de início de saída do trabalho** e, em seguida, selecione **Iniciar**.

Escolha uma das opções de **hora de início da saída do trabalho**. As opções *agora*são *personalizado*, e, se o trabalho foi executado anteriormente, *quando foi interrompido pela última vez*. Consulte acima para obter mais informações sobre essas opções.

## <a name="visual-studio"></a>Visual Studio

Na exibição de trabalho, selecione o botão de seta verde para iniciar o trabalho. Defina o **modo de início de saída do trabalho** e selecione **Iniciar**. O status do trabalho será alterado para **em execução**.

Há três opções para o **modo de início de saída do trabalho**: *JobStartTime*, *CustomTime*e *LastOutputEventTime*. Se essa propriedade estiver ausente, o padrão será *JobStartTime*. Consulte acima para obter mais informações sobre essas opções.


## <a name="powershell"></a>PowerShell

Use o cmdlet a seguir para iniciar seu trabalho usando o PowerShell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Há três opções para **OutputStartMode**: *JobStartTime*, *CustomTime*e *LastOutputEventTime*. Se essa propriedade estiver ausente, o padrão será *JobStartTime*. Consulte acima para obter mais informações sobre essas opções.

Para obter mais informações sobre `Start-AzStreamAnalyitcsJob` o cmdlet, exiba a [referência Start-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Passos Seguintes

* [Quickstart: Criar um trabalho de Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md)
* [Quickstart: Criar um trabalho de Stream Analytics usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Quickstart: Criar um trabalho de Stream Analytics usando o Azure Stream Analytics Tools para Visual Studio](stream-analytics-quick-create-vs.md)
