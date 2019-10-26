---
title: Conceitos de ponto de verificação e recuperação de reprodução no Azure Stream Analytics
description: Este artigo descreve os conceitos de ponto de verificação e de recuperação de trabalho de reprodução no Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 26d8d8248c9dcc57edaaa4a90f87071ee61a70ce
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935033"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Conceitos de ponto de verificação e reprodução em trabalhos de Azure Stream Analytics
Este artigo descreve os conceitos internos de ponto de verificação e reprodução no Azure Stream Analytics e o impacto que eles têm na recuperação do trabalho. Cada vez que um trabalho de Stream Analytics é executado, as informações de estado são mantidas internamente. Essas informações de estado são salvas em um ponto de verificação periodicamente. Em alguns cenários, as informações de ponto de verificação serão usadas para recuperação de trabalho se ocorrer uma falha de trabalho ou atualização. Em outras circunstâncias, o ponto de verificação não pode ser usado para recuperação e uma reprodução é necessária.

## <a name="stateful-query-logicin-temporal-elements"></a>Lógica de consulta com estado em elementos temporais
Um dos recursos exclusivos do trabalho Azure Stream Analytics é executar o processamento com estado, como agregações em janelas, junções temporais e funções analíticas temporais. Cada um desses operadores mantém informações de estado quando o trabalho é executado. O tamanho máximo da janela para esses elementos de consulta é de sete dias. 

O conceito de janela temporal aparece em vários elementos de consulta Stream Analytics:
1. Agregações em janela (grupo por de em cascata, salto e janelas deslizantes)

2. Junções temporais (junção com DATEDIFF)

3. Funções analíticas temporais (isprimeiro, último e retardo com limite de duração)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Recuperação de trabalho de falha de nó, incluindo atualização do so
Cada vez que um trabalho de Stream Analytics é executado, internamente, ele é escalado horizontalmente para fazer o trabalho entre vários nós de trabalho. O estado de cada nó de trabalho é verificado a cada poucos minutos, o que ajuda o sistema a se recuperar se ocorrer uma falha.

Às vezes, um determinado nó de trabalho pode falhar ou uma atualização do sistema operacional pode ocorrer para esse nó de trabalho. Para recuperar automaticamente, Stream Analytics adquire um novo nó íntegro e o estado anterior do nó de trabalho é restaurado do ponto de verificação mais recente disponível. Para retomar o trabalho, uma pequena quantidade de repetição é necessária para restaurar o estado a partir da hora em que o ponto de verificação é obtido. Normalmente, o intervalo de restauração é de apenas alguns minutos. Quando unidades de streaming suficientes são selecionadas para o trabalho, a reprodução deve ser concluída rapidamente. 

Em uma consulta totalmente paralela, o tempo necessário para acompanhar após a falha de um nó de trabalho é proporcional a:

[a taxa de eventos de entrada] x [o comprimento do intervalo]/[número de partições de processamento]

Se você observar um atraso significativo no processamento devido à falha do nó e à atualização do sistema operacional, considere tornar a consulta totalmente paralela e dimensione o trabalho para alocar mais unidades de streaming. Para obter mais informações, consulte [dimensionar um trabalho de Azure Stream Analytics para aumentar a taxa de transferência](stream-analytics-scale-jobs.md).

O Stream Analytics atual não mostra um relatório quando esse tipo de processo de recuperação está ocorrendo.

## <a name="job-recovery-from-a-service-upgrade"></a>Recuperação de trabalho de uma atualização de serviço 
Ocasionalmente, a Microsoft atualiza os binários que executam os trabalhos de Stream Analytics no serviço do Azure. Nesses momentos, os trabalhos em execução dos usuários são atualizados para a versão mais recente e o trabalho é reiniciado automaticamente. 

Atualmente, o formato do ponto de verificação de recuperação não é preservado entre atualizações. Como resultado, o estado da consulta de streaming deve ser totalmente restaurado usando a técnica de reprodução. Para permitir que Stream Analytics trabalhos reproduzam exatamente a mesma entrada de antes, é importante definir a política de retenção para os dados de origem para pelo menos os tamanhos de janela em sua consulta. Não fazer isso pode resultar em resultados incorretos ou parciais durante a atualização do serviço, pois os dados de origem podem não ser retidos muito o suficiente para incluir o tamanho completo da janela.

Em geral, a quantidade de repetição necessária é proporcional ao tamanho da janela multiplicada pela taxa média de eventos. Por exemplo, para um trabalho com uma taxa de entrada de 1000 eventos por segundo, um tamanho de janela maior que uma hora é considerado com um tamanho de reprodução grande. Até uma hora de dados talvez precisem ser reprocessados para inicializar o estado para que ele possa produzir resultados completos e corretos, o que pode causar uma saída atrasada (sem saída) por algum período estendido. Consultas sem Windows ou outros operadores temporais, como `JOIN` ou `LAG`, teriam zero reprodução.

## <a name="estimate-replay-catch-up-time"></a>Estimar tempo de atualização de reprodução
Para estimar o comprimento do atraso devido a uma atualização de serviço, você pode seguir esta técnica:

1. Carregue o Hub de eventos de entrada com dados suficientes para cobrir o maior tamanho de janela em sua consulta, na taxa de eventos esperados. O carimbo de data/hora dos eventos deve estar próximo ao tempo do relógio de parede durante esse período de tempo, como se fosse um feed de entrada ao vivo. Por exemplo, se você tiver uma janela de três dias em sua consulta, envie eventos para o Hub de eventos por três dias e continue a enviar eventos. 

2. Inicie o trabalho usando **agora** como a hora de início. 

3. Meça o tempo entre a hora de início e quando a primeira saída é gerada. O tempo é aproximado quanto atraso o trabalho incorrerá durante uma atualização de serviço.

4. Se o atraso for muito longo, tente particionar seu trabalho e aumentar o número do SUs, para que a carga seja distribuída para mais nós. Como alternativa, considere reduzir os tamanhos de janela em sua consulta e executar agregação adicional ou outro processamento com estado na saída produzida pelo trabalho de Stream Analytics no coletor downstream (por exemplo, usando o banco de dados SQL do Azure).

Para uma preocupação de estabilidade de serviço geral durante a atualização de trabalhos de missão crítica, considere a execução de trabalhos duplicados em regiões emparelhadas do Azure. Para obter mais informações, consulte [garantir a confiabilidade Stream Analytics trabalho durante atualizações de serviço](stream-analytics-job-reliability.md).

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>Recuperação de trabalho de uma parada e início iniciados pelo usuário
Para editar a sintaxe de consulta em um trabalho de streaming ou para ajustar entradas e saídas, o trabalho precisa ser interrompido para fazer as alterações e atualizar o design do trabalho. Nesses cenários, quando um usuário para o trabalho de streaming e o inicia novamente, o cenário de recuperação é semelhante à atualização de serviço. 

Os dados de ponto de verificação não podem ser usados para reiniciar um trabalho iniciado pelo usuário. Para estimar o atraso de saída durante tal reinicialização, use o mesmo procedimento conforme descrito na seção anterior e aplique uma mitigação semelhante se o atraso for muito longo.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre confiabilidade e escalabilidade, consulte estes artigos:
- [Tutorial: configurar alertas para trabalhos de Azure Stream Analytics](stream-analytics-set-up-alerts.md)
- [Dimensionar um trabalho de Azure Stream Analytics para aumentar a taxa de transferência](stream-analytics-scale-jobs.md)
- [Garantir a confiabilidade Stream Analytics trabalho durante atualizações de serviço](stream-analytics-job-reliability.md)
