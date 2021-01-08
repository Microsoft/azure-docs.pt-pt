---
title: Checkpoint e replay conceitos de recuperação em Azure Stream Analytics
description: Este artigo descreve o checkpoint e a repetição de conceitos de recuperação de emprego no Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: df31f8538bb9eabeca37fe4c52c4443fd447e415
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015323"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Checkpoint e replay conceitos em empregos Azure Stream Analytics
Este artigo descreve os conceitos de checkpoint interno e repetição no Azure Stream Analytics, e o impacto que estes têm na recuperação de emprego. Cada vez que um trabalho stream Analytics é executado, a informação do estado é mantida internamente. Essa informação do estado é guardada num posto de controlo periodicamente. Em alguns cenários, a informação do checkpoint é usada para a recuperação de emprego se ocorrer uma falha de trabalho ou atualização. Noutras circunstâncias, o ponto de verificação não pode ser utilizado para a recuperação, e é necessária uma repetição.

## <a name="stateful-query-logic-in-temporal-elements"></a>Lógica de consulta imponente em elementos temporais
Uma das capacidades únicas do trabalho do Azure Stream Analytics é realizar um processamento imponente, como agregados com janelas, juntas temporais e funções analíticas temporais. Cada um destes operadores mantém a informação do Estado quando o trabalho funciona. O tamanho máximo da janela para estes elementos de consulta é de sete dias. 

O conceito de janela temporal aparece em vários elementos de consulta stream analytics:
1. Agregados com janelas (GRUPO BY de Tumbling, Hopping e Janelas deslizantes)

2. Unições temporais (junte-se ao DATEDIFF)

3. Funções analíticas temporais (ISFIRST, LAST e LAG com DURAÇÃO LIMITE)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Recuperação de emprego da falha do nó, incluindo atualização do SO
Cada vez que um trabalho stream Analytics funciona, internamente é dimensionado para fazer trabalho em vários nós de trabalhadores. O estado de cada nó de trabalhador é posto em controlo a cada poucos minutos, o que ajuda o sistema a recuperar em caso de falha.

Por vezes, um dado nó de trabalhador pode falhar ou pode ocorrer uma atualização do Sistema Operativo para esse nó do trabalhador. Para recuperar automaticamente, o Stream Analytics adquire um novo nó saudável, e o estado do nó do trabalhador anterior é restaurado a partir do mais recente ponto de verificação disponível. Para retomar os trabalhos, uma pequena quantidade de repetição é necessária para restaurar o estado a partir do momento em que o ponto de verificação é tomado. Normalmente, a abertura de restauro é de apenas alguns minutos. Quando forem selecionadas unidades de streaming suficientes para o trabalho, a repetição deve ser concluída rapidamente. 

Numa consulta totalmente paralela, o tempo que leva para recuperar após a falha do nó do trabalhador é proporcional a:

[a taxa de entrada do evento] x [o comprimento da lacuna] / [número de divisórias de processamento]

Se alguma vez observar um atraso significativo de processamento devido à falha do nó e à atualização do SISTEMA, considere tornar a consulta totalmente paralela e escalar o trabalho para alocar mais Unidades de Streaming. Para obter mais informações, consulte [scale a Azure Stream Analytics para aumentar a produção](stream-analytics-scale-jobs.md).

O Stream Analytics atual não mostra um relatório quando este tipo de processo de recuperação está a decorrer.

## <a name="job-recovery-from-a-service-upgrade"></a>Recuperação de emprego de uma atualização de serviço 
A Microsoft atualiza ocasionalmente os binários que executam os trabalhos do Stream Analytics no serviço Azure. Nestes momentos, os trabalhos de funcionamento dos utilizadores são atualizados para a versão mais recente e o trabalho reinicia automaticamente. 

Atualmente, o formato de verificação de recuperação não é preservado entre upgrades. Como resultado, o estado da consulta de streaming deve ser restaurado inteiramente utilizando a técnica de repetição. Para permitir que os trabalhos do Stream Analytics reproduzam exatamente a mesma entrada de antes, é importante definir a política de retenção para os dados de origem para pelo menos os tamanhos das janelas na sua consulta. Se não o fizer, poderá resultar em resultados incorretos ou parciais durante a atualização do serviço, uma vez que os dados de origem podem não ser retidos o suficiente para incluir o tamanho total da janela.

Em geral, a quantidade de repetição necessária é proporcional ao tamanho da janela multiplicada pela taxa média de evento. Como exemplo, para um trabalho com uma taxa de entrada de 1000 eventos por segundo, um tamanho de janela superior a uma hora é considerado como tendo um grande tamanho de repetição. Até uma hora de dados podem ter de ser reencamêndidos para inicializar o estado para que possa produzir resultados completos e corretos, o que pode causar uma saída atrasada (sem saída) durante algum período prolongado. Consultas sem janelas ou outros operadores temporais, como `JOIN` `LAG` ou, não teriam nenhuma repetição.

## <a name="estimate-replay-catch-up-time"></a>Estimativa do tempo de recuperação de repetição
Para estimar a duração do atraso devido a uma atualização de serviço, pode seguir esta técnica:

1. Carregue o Hub de Eventos de entrada com dados suficientes para cobrir o maior tamanho da janela na sua consulta, à taxa de evento esperada. A marca de tempo dos eventos deve estar perto do tempo do relógio de parede durante esse período de tempo, como se fosse um feed de entrada ao vivo. Por exemplo, se tiver uma janela de 3 dias na sua consulta, envie eventos para o Event Hub durante três dias e continue a enviar eventos. 

2. Comece o trabalho usando **Agora** como a hora de início. 

3. Meça o tempo entre a hora de início e a primeira saída. O tempo é difícil quanto atraso o trabalho incorreria durante uma atualização de serviço.

4. Se o atraso for muito longo, tente dividir o seu trabalho e aumentar o número de SUs, para que a carga seja distribuída para mais nós. Em alternativa, considere reduzir os tamanhos das janelas na sua consulta e efetuar uma maior agregação ou outro processamento imponente na saída produzida pelo trabalho Stream Analytics na pia a jusante (por exemplo, utilizando a Base de Dados Azure SQL).

Para a preocupação geral de estabilidade do serviço durante a atualização de empregos críticos da missão, considere a execução de empregos duplicados em regiões de Azure em pares. Para obter mais informações, consulte [a fiabilidade do trabalho do Guarantee Stream Analytics durante as atualizações de serviço](stream-analytics-job-reliability.md).

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>Recuperação de emprego de um utilizador iniciado paragem e início
Para editar a sintaxe de consulta num trabalho de streaming, ou para ajustar entradas e saídas, o trabalho precisa de ser interrompido para fazer as alterações e atualizar o design do trabalho. Nestes cenários, quando um utilizador para o trabalho de streaming e recomeça, o cenário de recuperação é semelhante ao upgrade do serviço. 

Os dados do checkpoint não podem ser utilizados para um reinício de trabalho iniciado pelo utilizador. Para estimar o atraso da produção durante este reinício, utilize o mesmo procedimento descrito na secção anterior e aplique uma mitigação semelhante se o atraso for demasiado longo.

## <a name="next-steps"></a>Próximos passos
Para obter mais informações sobre fiabilidade e escalabilidade, consulte estes artigos:
- [Tutorial: Criar alertas para trabalhos do Azure Stream Analytics](stream-analytics-set-up-alerts.md)
- [Escalar um trabalho Azure Stream Analytics para aumentar a produção](stream-analytics-scale-jobs.md)
- [Garantia Stream Analytics fiabilidade do trabalho durante atualizações de serviço](stream-analytics-job-reliability.md)
