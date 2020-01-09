---
title: Conceitos de ponto de verificação e recuperação de reprodução no Azure Stream Analytics
description: Este artigo descreve o ponto de verificação e repetição conceitos de recuperação de tarefa no Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: f5bb2b97d7da770828c2f4f03167483ad2044c79
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426397"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Conceitos de ponto de verificação e repetição em tarefas do Azure Stream Analytics
Este artigo descreve os internos ponto de verificação e repetição conceitos no Azure Stream Analytics e o impacto que aqueles tem na recuperação da tarefa. Sempre que é executada uma tarefa do Stream Analytics, informações de estado são mantidas internamente. Que informações de estado são salvas num ponto de verificação periodicamente. Em alguns cenários, as informações de ponto de verificação são utilizadas para a recuperação de tarefa se ocorrer uma falha da tarefa ou a atualização. Em outras circunstâncias, o ponto de verificação não pode ser utilizado para recuperação e uma repetição é necessária.

## <a name="stateful-query-logicin-temporal-elements"></a>Lógica de consulta com monitoração de Estado nos elementos temporais
Um da capacidade única de trabalho do Azure Stream Analytics consiste em efetuar o processamento com monitoração de estado, tais como funções de análise temporais, associações temporais e agregados em janelas. Cada um destes operadores mantém informações de estado quando a tarefa é executada. O tamanho máximo da janela para esses elementos de consulta é de sete dias. 

O conceito de janela temporal é apresentado em vários elementos de consulta do Stream Analytics:
1. Agregados em janelas (grupo por de em cascata, saltos e deslizante windows)

2. Associações temporais (associação com DATEDIFF)

3. Funções de análise temporais (ISFIRST, LAST e desfasamento com duração de limite)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Recuperação de tarefa de falha de nó, incluindo a atualização do SO
Sempre que for executada uma tarefa do Stream Analytics, internamente ele é dimensionado para fazer o trabalho em vários nós de trabalho. Estado de cada nó de trabalho é foi efetuada a verificação intervalos de poucos minutos, que ajuda o recuperar se ocorrer uma falha de sistema.

Às vezes, poderá falhar um nó de trabalho de determinada ou uma atualização do sistema operativo pode ocorrer para esse nó de trabalho. Para recuperar automaticamente, o Stream Analytics adquire um novo nó de bom estado de funcionamento e estado do nó de trabalho anterior é restaurado a partir do mais recente disponível ponto de verificação. Para retomar o trabalho, uma pequena quantidade de repetição é necessária para restaurar o estado a partir da hora de quando o ponto de verificação é tirado. Normalmente, a lacuna de restauro é apenas alguns minutos. Quando o suficiente unidades de transmissão em fluxo são selecionadas para a tarefa, a repetição deve ser concluída rapidamente. 

Numa consulta paralela completamente, o tempo que demora a recuperar-se após uma falha de nó de trabalho é proporcional ao:

[a taxa de eventos de entrada] x [o comprimento de lacuna] / [o número de partições de processamento]

Se alguma vez observar o atraso de processamento significativo devido a falha de nó e de atualização de SO, considere fazer a consulta totalmente em paralelo e dimensionar a tarefa para alocar mais unidades de transmissão em fluxo. Para obter mais informações, consulte [dimensionar uma tarefa Azure Stream Analytics para aumentar o débito](stream-analytics-scale-jobs.md).

Atual Stream Analytics não mostra um relatório quando esse tipo de processo de recuperação estiver em curso.

## <a name="job-recovery-from-a-service-upgrade"></a>Recuperação de tarefa de uma atualização de serviço 
Microsoft, ocasionalmente, atualiza os binários que executam tarefas do Stream Analytics no serviço do Azure. Estes tempos, tarefas de execução dos utilizadores são atualizadas para a versão mais recente e a tarefa é reiniciado automaticamente. 

Atualmente, o formato de ponto de verificação de recuperação não é mantido entre as atualizações. Como resultado, o estado da consulta de transmissão em fluxo deve ser restaurado utilizando inteiramente os técnica de repetição. Para permitir que as tarefas de Stream Analytics reproduzir exatamente iguais de entrada de antes, é importante definir a política de retenção para a origem de dados para, pelo menos, a janela de tamanhos de na sua consulta. Caso contrário, pode resultar em resultados parciais ou incorretos durante a atualização de serviço, uma vez que a origem de dados não pode ser mantidas suficientemente novamente para incluir o tamanho da janela completa.

Em geral, a quantidade de repetição necessária é proporcional ao tamanho da janela multiplicado pela taxa de eventos média. Por exemplo, para uma tarefa com uma taxa de entrada de 1000 eventos por segundo, um tamanho de janela maior do que uma hora é considerado têm um tamanho grande de repetição. Até uma hora de dados poderá ter de ser novamente processados para inicializar o estado, para que este possa produzir completa e resultados corretos, o que podem causar um atraso (nenhuma saída) de saída durante um período prolongado. As consultas com nenhuma windows ou outros operadores temporais, como `JOIN` ou `LAG`, teria de repetição de zero.

## <a name="estimate-replay-catch-up-time"></a>Tempo de catch-up de repetição de estimativa
Para calcular o comprimento do atraso devido a uma atualização de serviço, pode seguir essa técnica:

1. Carregar a entrada Hub de eventos com dados suficientes para cobrir o maior tamanho de janela na sua consulta, a taxa de evento esperado. Timestamp dos eventos deve ser próximo da hora de relógio de parede durante esse período de tempo, como se se trata de uma feed de entrada em direto. Por exemplo, se tiver uma janela de 3 dias na sua consulta, enviar eventos para o Hub de eventos para três dias e continue a enviar eventos. 

2. Começar a utilizar a tarefa **agora** como a hora de início. 

3. Medir o tempo entre a hora de início e quando é gerado o primeiro resultado. O tempo é aproximada quanto a tarefa serão cobrados durante uma atualização do serviço de atraso.

4. Se o atraso é demasiado longo, tente a tarefa de partição e aumentar o número de SUs, pelo que a carga é feita para mais nós. Em alternativa, considere reduzir o tamanho de janela na sua consulta e efetuar a agregação ou outra com monitoração de estado de processamento na saída produzida pela tarefa de Stream Analytics no coletor downstream (por exemplo, com a base de dados SQL do Azure).

Para preocupação de estabilidade do serviço geral durante a atualização das tarefas de missão crítica, considere a execução de trabalhos de duplicados em regiões emparelhadas do Azure. Para obter mais informações, consulte [fiabilidade da tarefa de garantia de Stream Analytics durante as atualizações de serviço](stream-analytics-job-reliability.md).

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>Recuperação de tarefa de um usuário iniciou pare e inicie
Para editar a sintaxe de consulta numa tarefa de transmissão em fluxo, ou para ajustar as entradas e saídas, a tarefa tem de ser parado para fazer as alterações e atualizar o design de tarefa. Em tais cenários, quando um usuário interrompe a tarefa de transmissão em fluxo e inicia-o novamente, o cenário de recuperação é semelhante à atualização de serviço. 

Dados de ponto de verificação não podem ser utilizados para um reinício de tarefa iniciada pelo utilizador. Para estimar o atraso de saída durante um reinício desse tipo, utilize o mesmo procedimento conforme descrito na secção anterior e aplicar a atenuação semelhante, se o atraso é demasiado longo.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a fiabilidade e escalabilidade, veja estes artigos:
- [Tutorial: Configurar alertas para tarefas do Azure Stream Analytics](stream-analytics-set-up-alerts.md)
- [Dimensionar uma tarefa Azure Stream Analytics para aumentar o débito](stream-analytics-scale-jobs.md)
- [Garante a confiabilidade de tarefa do Stream Analytics durante as atualizações de serviço](stream-analytics-job-reliability.md)
