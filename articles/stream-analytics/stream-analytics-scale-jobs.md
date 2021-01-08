---
title: Escalar e sair em empregos Azure Stream Analytics
description: Este artigo descreve como escalar um trabalho stream Analytics dividindo dados de entrada, sintonizando a consulta e definindo unidades de streaming de emprego.
author: JSeb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: e3d4fd6b6b83681284278d10409a1c16394db31f
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018688"
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>Escalar um trabalho Azure Stream Analytics para aumentar a produção
Este artigo mostra-lhe como sintonizar uma consulta stream Analytics para aumentar a produção para trabalhos de Streaming Analytics. Pode utilizar o seguinte guia para escalar o seu trabalho para lidar com cargas mais elevadas e tirar partido de mais recursos do sistema (como mais largura de banda, mais recursos de CPU, mais memória).
Como pré-requisito, poderá ter de ler os seguintes artigos:
-   [Compreender e ajustar as Unidades de Transmissão em Fluxo](stream-analytics-streaming-unit-consumption.md)
-   [Criar empregos paralelos](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Caso 1 - A sua consulta é inerentemente totalmente paralestozável através de divisórias de entrada
Se a sua consulta for inerentemente totalmente paralizável entre divisórias de entrada, pode seguir os seguintes passos:
1.  Autore a sua consulta para ser embaraçosamente paralela utilizando **a palavra-chave PARTITION BY.** Veja mais detalhes na secção de trabalhos paralelos [embaraçosos nesta página.](stream-analytics-parallelization.md)
2.  Dependendo dos tipos de saída utilizados na sua consulta, alguma saída pode não ser paralesticamente, ou precisar de configuração adicional para ser embaraçosamente paralela. Por exemplo, a saída do PowerBI não é paralessível. As saídas são sempre fundidas antes de enviar para o lavatório de saída. Blobs, Tabelas, ADLS, Service Bus e Azure Function são automaticamente paralelos. As saídas SQL e Azure Synapse Analytics têm uma opção para a paralelização. O Event Hub precisa de ter a configuração PartitionKey definida para combinar com o campo **PARTITION BY** (normalmente PartitionId). Para o Event Hub, também prestem mais atenção para corresponder ao número de divisórias para todas as entradas e todas as saídas para evitar o cruzamento entre divisórias. 
3.  Faça a sua consulta com **6 SU** (que é a capacidade total de um único nó de computação) para medir o máximo de rendimento alcançável, e se estiver a usar **o GRUPO BY,** meça quantos grupos (cardinalidade) o trabalho pode suportar. Os sintomas gerais do trabalho que atinge os limites de recursos do sistema são os seguintes.
    - A métrica de utilização su % é superior a 80%. Isto indica que o uso da memória é elevado. Os fatores que contribuem para o aumento desta métrica são [aqui](stream-analytics-streaming-unit-consumption.md)descritos. 
    -   O tempo de saída está a ficar para trás no que diz respeito à hora do relógio de parede. Dependendo da sua lógica de consulta, o tempo de saída pode ter uma lógica compensada a partir da hora do relógio de parede. No entanto, deverão progredir aproximadamente ao mesmo ritmo. Se o tempo de saída estiver a ficar cada vez mais para trás, é um indicador de que o sistema está a trabalhar em excesso. Pode ser resultado de um estrangulamento da pia de saída a jusante, ou de uma elevada utilização do CPU. Não fornecemos métrica de utilização de CPU neste momento, por isso pode ser difícil diferenciar os dois.
        - Se o problema for devido ao estrangulamento do afundamento, poderá ser necessário aumentar o número de divisórias de saída (e também inserir divisórias para manter o trabalho totalmente paralelizável), ou aumentar a quantidade de recursos do lavatório (por exemplo, número de Unidades de Pedido para CosmosDB).
    - No diagrama de trabalho, há uma métrica de evento por partição para cada entrada. Se a métrica do evento de atraso continuar a aumentar, é também um indicador de que o recurso do sistema está limitado (seja por causa do estrangulamento do lavatório de saída, ou do ALTO CPU).
4.  Uma vez determinados os limites do que um trabalho de 6 SU pode alcançar, você pode extrapolar linearmente a capacidade de processamento do trabalho à medida que adiciona mais SUs, assumindo que não tem qualquer distorção de dados que torne certas divisões "quentes".

> [!NOTE]
> Escolha o número certo de Unidades de Streaming: Como o Stream Analytics cria um nó de processamento para cada 6 SU adicionados, o melhor é fazer do número de nós uma divisor do número de divisórias de entrada, para que as divisórias possam ser distribuídas uniformemente pelos nós.
> Por exemplo, mediu o seu trabalho de 6 SU pode atingir 4 MB/s de taxa de processamento, e a sua contagem de divisórias de entrada é de 4. Pode optar por executar o seu trabalho com 12 SU para obter aproximadamente 8 MB/s de taxa de processamento, ou 24 SU para atingir 16 MB/s. Em seguida, pode decidir quando aumentar o número de SU para o trabalho a que valor, em função da sua taxa de entrada.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Caso 2 - Se a sua consulta não for embaraçosamente paralela.
Se a sua consulta não for embaraçosamente paralela, pode seguir os seguintes passos.
1.  Comece com uma consulta sem **PARTIÇÃO BY** primeiro para evitar a complexidade da partição, e executar a sua consulta com 6 SU para medir a carga máxima como no [caso 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  Se conseguires atingir a carga prevista em prazo de produção, estás feito. Em alternativa, pode optar por medir o mesmo trabalho a funcionar a 3 SU e 1 SU, para descobrir o número mínimo de SU que funciona para o seu cenário.
3.  Se não conseguir alcançar a produção desejada, tente dividir a sua consulta em vários passos, se possível, se já não tiver vários passos, e aloque até 6 SU para cada passo na consulta. Por exemplo, se tiver 3 passos, aloque 18 SU na opção "Escala".
4.  Ao executar tal trabalho, a Stream Analytics coloca cada passo no seu próprio nó com 6 recursos de SU dedicados. 
5.  Se ainda não atingiu o seu alvo de carga, pode tentar utilizar a **PARTIÇÃO** partindo dos passos mais próximos da entrada. Para o operador **group by** que pode não ser naturalmente partitionável, pode utilizar o padrão agregado local/global para executar um GRUPO dividido **por** seguido por um grupo não dividido **BY**. Por exemplo, se quiser contar quantos carros passam por cada portagem a cada 3 minutos, e o volume dos dados está para além do que pode ser manuseado por 6 SU.

Consulta:

 ```SQL
 WITH Step1 AS (
 SELECT COUNT(*) AS Count, TollBoothId, PartitionId
 FROM Input1 Partition By PartitionId
 GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
 )
 SELECT SUM(Count) AS Count, TollBoothId
 FROM Step1
 GROUP BY TumblingWindow(minute, 3), TollBoothId
 ```
Na consulta acima, você está contando carros por portagem por partção, e, em seguida, adicionando a contagem de todas as divisórias juntas.

Uma vez dividida, para cada divisão do degrau, alocar até 6 SU, cada divisória com 6 SU é o máximo, para que cada partição possa ser colocada no seu próprio nó de processamento.

> [!Note]
> Se a sua consulta não puder ser dividida, adicionar SU adicional numa consulta de vários passos pode nem sempre melhorar a produção. Uma forma de ganhar desempenho é reduzir o volume nos passos iniciais usando o padrão agregado local/global, como descrito acima no passo 5.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Caso 3 - Você está executando muitas consultas independentes em um trabalho.
Para certos casos de utilização do ISV, em que é mais rentável processar dados de vários inquilinos num único trabalho, utilizando entradas e saídas separadas para cada inquilino, você pode acabar executando algumas (por exemplo 20) consultas independentes em um único trabalho. O pressuposto é que cada carga de cada sublote é relativamente pequena. Neste caso, pode seguir os seguintes passos.
1.  Neste caso, não utilize **a PARTIÇÃO BY** na consulta
2.  Reduza a contagem de partição de entrada para o valor mais baixo possível de 2 se estiver a utilizar o Event Hub.
3.  Executar a consulta com 6 SU. Com a carga esperada para cada subquery, adicione o maior número possível de subqueries, até que o trabalho esteja a atingir os limites de recursos do sistema. Consulte o [Caso 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) para os sintomas quando isto acontecer.
4.  Uma vez atingido o limite de subquery medido acima, comece a adicionar o sublotey a um novo emprego. O número de empregos a executar em função do número de consultas independentes deve ser bastante linear, assumindo que não tem qualquer distorção de carga. Em seguida, pode prever quantos 6 empregos de 2ºC precisa de executar em função do número de inquilinos que gostaria de servir.
5.  Ao utilizar dados de referência, junte-se a essas consultas, una as entradas antes de se juntar aos mesmos dados de referência. Então, divida os eventos, se necessário. Caso contrário, cada data de referência junta-se mantém uma cópia de dados de referência na memória, provavelmente explodindo o uso da memória desnecessariamente.

> [!Note] 
> Quantos inquilinos colocar em cada trabalho?
> Este padrão de consulta tem frequentemente um grande número de subqueries, e resulta em topologia muito grande e complexa. O controlador do trabalho pode não ser capaz de lidar com uma topologia tão grande. Em regra, fique abaixo de 40 inquilinos para 1 emprego de SU, e 60 inquilinos para 3 empregos de SU e 6 SU. Quando estiver a exceder a capacidade do controlador, o trabalho não começará com sucesso.



## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente o nosso [Microsoft Q&Uma página de perguntas para a Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Próximos passos
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Referência do idioma de consulta do Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](/rest/api/streamanalytics/)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: /previous-versions/azure/dn789972(v=azure.100)

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/