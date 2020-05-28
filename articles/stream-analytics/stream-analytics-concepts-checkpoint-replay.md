---
title: Checkpoint e reproduzir conceitos de recuperação no Azure Stream Analytics
description: Este artigo descreve conceitos de checkpoint e repetição de recuperação de empregos no Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 10d9053e082a995085fa255cc0d9f63a2b4e2b17
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020613"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Conceitos de checkpoint e repetição em empregos de Azure Stream Analytics
Este artigo descreve os conceitos internos de checkpoint e replay no Azure Stream Analytics, e o impacto que estes têm na recuperação de emprego. Cada vez que um trabalho da Stream Analytics corre, a informação do Estado é mantida internamente. Essa informação do estado é guardada num posto de controlo periodicamente. Em alguns cenários, a informação do checkpoint é usada para a recuperação de emprego se ocorrer uma falha de emprego ou upgrade. Noutras circunstâncias, o ponto de verificação não pode ser utilizado para a recuperação, e é necessária uma repetição.

## <a name="stateful-query-logicin-temporal-elements"></a>Lógica de consulta imponente em elementos temporais
Uma das capacidades únicas do trabalho do Azure Stream Analytics é realizar um processamento audato, como agregados com janelas, juntas temporais e funções analíticas temporais. Cada um destes operadores guarda informações estatais quando o trabalho funciona.O tamanho máximo da janela para estes elementos de consulta é de sete dias. 

O conceito de janela temporal aparece em vários elementos de consulta stream analytics:
1. Agregados com janelas (GROUP BY de janelas tumbling, hopping e sliding)

2. Juntas temporais (JUNTE-SE com DATEDIFF)

3. Funções analíticas temporais (ISFIRST, LAST e LAG com DURAÇÃO LIMITE)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Recuperação de emprego de falha no nó, incluindo atualização do SO
Cada vez que um trabalho de Stream Analytics corre, internamente é escalado para fazer trabalho em vários nós de trabalhadores. O estado de cada nó de trabalhador é posto de controlo a cada poucos minutos, o que ajuda o sistema a recuperar se ocorrer uma falha.

Por vezes, um determinado nó de trabalhador pode falhar, ou pode ocorrer uma atualização do Sistema Operativo para esse nó de trabalhador. Para recuperar automaticamente, o Stream Analytics adquire um novo nó saudável, e o estado do nó de trabalhador anterior é restaurado a partir do mais recente ponto de verificação disponível. Para retomar o trabalho, é necessária uma pequena quantidade de repetição para restaurar o estado a partir do momento em que o posto de controlo é tomado. Normalmente, a lacuna de restauro é de apenas alguns minutos. Quando forem selecionadas unidades de streaming suficientes para o trabalho, a repetição deve ser concluída rapidamente. 

Numa consulta totalmente paralela, o tempo que demora a recuperar depois de uma falha no nó do trabalhador é proporcional a:

[a taxa de evento de entrada] x [o comprimento da lacuna] / [número de divisórias de processamento]

Se observar um atraso significativo de processamento devido à falha no nó e à atualização do OS, considere tornar a consulta totalmente paralela e dimensionar o trabalho para alocar mais Unidades de Streaming. Para mais informações, consulte [scale a Azure Stream Analytics para aumentar a entrada](stream-analytics-scale-jobs.md).

O Stream Analytics atual não mostra um relatório quando este tipo de processo de recuperação está a decorrer.

## <a name="job-recovery-from-a-service-upgrade"></a>Recuperação de emprego de uma atualização de serviço 
A Microsoft ocasionalmente atualiza os binários que executam os trabalhos do Stream Analytics no serviço Azure. Nestes momentos, os postos de trabalho dos utilizadores são atualizados para versão mais recente e o trabalho reinicia automaticamente. 

Atualmente, o formato de controlo de recuperação não é preservado entre atualizações. Como resultado, o estado da consulta de streaming deve ser restaurado inteiramente usando a técnica de reprodução. De forma a permitir que os trabalhos do Stream Analytics reproduzam a mesma entrada de antes, é importante definir a política de retenção dos dados de origem para, pelo menos, os tamanhos da janela na sua consulta. Se não o fizer, pode resultar em resultados incorretos ou parciais durante a atualização do serviço, uma vez que os dados de origem podem não ser retidos suficientemente para trás para incluir o tamanho total da janela.

Em geral, a quantidade de repetição necessária é proporcional ao tamanho da janela multiplicada pela taxa média de eventos. Como exemplo, para um trabalho com uma taxa de entrada de 1000 eventos por segundo, um tamanho de janela superior a uma hora é considerado como tendo um grande tamanho de repetição. Até uma hora de dados podem ter de ser reprocessados para inicializar o estado para que possa produzir resultados completos e corretos, o que pode causar uma saída retardada (sem saída) durante algum período prolongado. Consultas sem janelas ou outros operadores temporais, como `JOIN` `LAG` ou, teriam zero repetição.

## <a name="estimate-replay-catch-up-time"></a>Estimar tempo de recuperação de reprodução
Para estimar a duração do atraso devido a uma atualização de serviço, pode seguir esta técnica:

1. Carregue o Input Event Hub com dados suficientes para cobrir o maior tamanho da janela na sua consulta, à taxa de evento esperada. O carimbo de tempo dos eventos deve estar próximo do tempo do relógio da parede durante todo esse período de tempo, como se fosse um feed de entrada ao vivo. Por exemplo, se tiver uma janela de 3 dias na sua consulta, envie eventos para o Event Hub durante três dias e continue a enviar eventos. 

2. Comece o trabalho usando **agora** como a hora de início. 

3. Meça o tempo entre o tempo de início e quando a primeira saída for gerada. O tempo é difícil quanto atraso o trabalho incorreria durante uma atualização de serviço.

4. Se o atraso for demasiado longo, tente dividir o seu trabalho e aumente o número de US, para que a carga seja distribuída para mais nós. Alternativamente, considere reduzir os tamanhos das janelas na sua consulta e efetuar uma maior agregação ou outro processamento audatório na saída produzida pelo trabalho stream analytics no sumidouro a jusante (por exemplo, utilizando a Base de Dados Azure SQL).

Para a preocupação com a estabilidade do serviço geral durante a atualização dos postos de trabalho críticos da missão, considere a realização de trabalhos duplicados em regiões de Azure emparelhadas. Para mais informações, consulte a fiabilidade do trabalho do [Guarantee Stream Analytics durante as atualizações](stream-analytics-job-reliability.md)de serviço .

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>Recuperação de emprego de um utilizador iniciado paragem e início
Para editar a sintaxe de Consulta num trabalho de streaming, ou para ajustar as inputs e saídas, o trabalho precisa de ser interrompido para fazer as alterações e melhorar o design de trabalho. Nestes cenários, quando um utilizador para o trabalho de streaming, e reinicia-o, o cenário de recuperação é semelhante ao upgrade de serviço. 

Os dados do checkpoint não podem ser utilizados para um reinício de trabalho iniciado pelo utilizador. Para estimar o atraso da produção durante esse reinício, utilize o mesmo procedimento descrito na secção anterior e aplique uma mitigação semelhante se o atraso for demasiado longo.

## <a name="next-steps"></a>Próximos passos
Para obter mais informações sobre fiabilidade e escalabilidade, consulte estes artigos:
- [Tutorial: Criar alertas para empregos da Azure Stream Analytics](stream-analytics-set-up-alerts.md)
- [Escala um trabalho de Azure Stream Analytics para aumentar a sua entrada](stream-analytics-scale-jobs.md)
- [Garantia Stream Analytics fiabilidade do trabalho durante atualizações de serviço](stream-analytics-job-reliability.md)
