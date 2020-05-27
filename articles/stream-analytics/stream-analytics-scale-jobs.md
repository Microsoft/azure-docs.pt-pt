---
title: Escalonamento e saída em empregos de Azure Stream Analytics
description: Este artigo descreve como escalar um trabalho de Stream Analytics dividindo dados de entrada, afinando a consulta e definindo unidades de streaming de trabalho.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: d982cc94a9ab0517d6453a30371635c1e3100676
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835602"
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>Escala um trabalho de Azure Stream Analytics para aumentar a sua entrada
Este artigo mostra-lhe como afinar uma consulta de Stream Analytics para aumentar a entrada para trabalhos de Streaming Analytics. Pode utilizar o seguinte guia para escalar o seu trabalho para lidar com uma carga mais elevada e tirar partido de mais recursos do sistema (como mais largura de banda, mais recursos cpu, mais memória).
Como pré-requisito, poderá ter de ler os seguintes artigos:
-   [Compreender e ajustar as Unidades de Transmissão em Fluxo](stream-analytics-streaming-unit-consumption.md)
-   [Criar empregos paralelizáveis](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Caso 1 - A sua consulta é inerentemente totalmente paralela através das divisórias de entrada
Se a sua consulta for inerentemente totalmente paralela através das divisórias de entrada, pode seguir os seguintes passos:
1.  Autor da sua consulta para ser embaraçosamente paralelo utilizando a **palavra-chave PARTITION BY.** Veja mais detalhes na secção de empregos embaraçosamente paralelos [nesta página](stream-analytics-parallelization.md).
2.  Dependendo dos tipos de saída utilizados na sua consulta, alguma saída pode não ser paralela, ou precisar de uma configuração adicional para ser embaraçosamente paralela. Por exemplo, a saída powerBI não é paralela. As saídas são sempre fundidas antes de enviar para o lavatório de saída. Bolhas, Mesas, ADLS, Service Bus e Função Azure são automaticamente paralelizados. As saídas SQL e SQL DW têm uma opção para paraletização. O Event Hub precisa de ter a configuração PartitionKey definida para combinar com o campo **PARTITION BY** (normalmente PartitionId). Para o Event Hub, preste também uma atenção extra para corresponder ao número de divisórias para todas as inputs e todas as saídas para evitar o cruzamento entre divisórias. 
3.  Faça a sua consulta com **6 SU** (que é a capacidade total de um único nó de computação) para medir o máximo de produção possível, e se estiver a usar o **GROUP BY,** meça quantos grupos (cardinalícia) o trabalho pode suportar. Os sintomas gerais do trabalho que atinge os limites de recursos do sistema são os seguintes.
    - A métrica de utilização da SU % é superior a 80%. Isto indica que o uso da memória é elevado. Os fatores que contribuem para o aumento desta métrica são descritos [aqui.](stream-analytics-streaming-unit-consumption.md) 
    -   O carimbo de tempo de saída está a ficar para trás no que diz respeito ao tempo do relógio da parede. Dependendo da sua lógica de consulta, o carimbo de tempo de saída pode ter uma lógica compensada a partir do tempo do relógio da parede. No entanto, deverão progredir aproximadamente à mesma taxa. Se o intervalo de tempo de saída está a ficar cada vez mais para trás, é um indicador de que o sistema está a funcionar em excesso. Pode ser resultado de estrangulamento da corrente a jusante ou de uma alta utilização do CPU. Não fornecemos a métrica de utilização do CPU neste momento, por isso pode ser difícil diferenciar os dois.
        - Se o problema se deve ao estrangulamento do sumidouro, poderá ser necessário aumentar o número de divisórias de saída (e também divisórias de entrada para manter o trabalho totalmente paralelo), ou aumentar a quantidade de recursos do lavatório (por exemplo, número de Unidades de Pedido para cosmosDB).
    - No diagrama de trabalho, há uma métrica de evento de atraso por divisória para cada entrada. Se a métrica do evento de atraso continuar a aumentar, é também um indicador de que o recurso do sistema está limitado (seja devido ao estrangulamento do sumidouro da saída, quer ao alto CPU).
4.  Uma vez determinados os limites do que um trabalho de 6 SU pode alcançar, pode extrapolar linearmente a capacidade de processamento do trabalho à medida que adiciona mais SUs, assumindo que não tem nenhum desvio de dados que torne certas divisórias "quentes".

> [!NOTE]
> Escolha o número certo de Unidades de Streaming: Como o Stream Analytics cria um nó de processamento para cada 6 SU adicionados, o melhor é fazer do número de nós um divisor do número de divisórias de entrada, para que as divisórias possam ser distribuídas uniformemente pelos nós.
> Por exemplo, mediu que o seu trabalho de 6 SU pode atingir uma taxa de processamento de 4 MB/s, e a sua contagem de partição de entrada é de 4. Pode optar por executar o seu trabalho com 12 SU para atingir aproximadamente 8 MB/s de processamento, ou 24 SU para atingir 16 MB/s. Em seguida, pode decidir quando aumentar o número de SU para o trabalho em que valor, em função da sua taxa de entrada.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Caso 2 - Se a sua consulta não for embaraçosamente paralela.
Se a sua consulta não for embaraçosamente paralela, pode seguir os seguintes passos.
1.  Comece com uma consulta sem **partição primeiro** para evitar a complexidade da divisão, e execute a sua consulta com 6 SU para medir a carga máxima como no [caso 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  Se conseguir esquelhá-lo em termos de entrada, está feito. Em alternativa, pode optar por medir o mesmo emprego a funcionar em 3 SU e 1 SU, para descobrir o número mínimo de SU que funciona para o seu cenário.
3.  Se não conseguir alcançar a entrada desejada, tente quebrar a sua consulta em vários passos, se possível, se ainda não tiver vários passos, e aloque até 6 SU por cada passo na consulta. Por exemplo, se tiver 3 passos, aloque 18 SU na opção "Escala".
4.  Ao executar tal trabalho, a Stream Analytics coloca cada passo no seu próprio nó com 6 recursos de SU dedicados. 
5.  Se ainda não atingiu o seu alvo de carga, pode tentar utilizar a **PARTIÇÃO** a partir de passos mais próximos da entrada. Para o operador **group BY** que pode não ser naturalmente divisor, pode utilizar o padrão agregado local/global para realizar um **GRUPO BY** dividido seguido de um GRUPO NÃO dividido **BY**. Por exemplo, se quiser contar quantos carros passam por cada portagem a cada 3 minutos, e o volume dos dados está além do que pode ser tratado por 6 SU.

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
Na consulta acima, você está contando carros por portagem por partição, e, em seguida, adicionando a contagem de todas as divisórias juntas.

Uma vez dividida, para cada partição do passo, alocar até 6 SU, cada partição com 6 SU é o máximo, para que cada partição possa ser colocada no seu próprio nó de processamento.

> [!Note]
> Se a sua consulta não puder ser dividida, adicionar SU adicional numa consulta de vários passos pode nem sempre melhorar a entrada. Uma forma de obter desempenho é reduzir o volume nos passos iniciais usando o padrão agregado local/global, como descrito acima no passo 5.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Caso 3 - Está a fazer muitas consultas independentes num trabalho.
Para certos casos de uso do ISV, onde é mais rentável processar dados de vários inquilinos num único trabalho, usando entradas e saídas separadas para cada inquilino, você pode acabar executando algumas (por exemplo 20) consultas independentes em um único trabalho. A suposição é que cada uma dessas subquerias é relativamente pequena. Neste caso, pode seguir os seguintes passos.
1.  Neste caso, não utilize **partition BY** na consulta
2.  Reduza a contagem de divisórias de entrada para o valor mais baixo possível de 2 se estiver a utilizar o Event Hub.
3.  Execute a consulta com 6 SU. Com a carga esperada para cada subquery, adicione o maior número possível de subqueries, até que o trabalho atinja os limites de recursos do sistema. Consulte o [Caso 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) para os sintomas quando isto acontecer.
4.  Uma vez atingido o limite de subquery medido acima, comece a adicionar o subquery a um novo emprego. O número de postos de trabalho a funcionar em função do número de consultas independentes deve ser bastante linear, assumindo que não tem qualquer distorção de carga. Pode então prever quantos 6 postos de trabalho na SU precisa de executar em função do número de inquilinos que gostaria de servir.
5.  Ao utilizar dados de referência, junte as inputs antes de se juntar aos mesmos dados de referência. Então, divida os eventos, se necessário. Caso contrário, cada adesão de dados de referência mantém uma cópia dos dados de referência na memória, provavelmente explodindo o uso da memória desnecessariamente.

> [!Note] 
> Quantos inquilinos colocar em cada trabalho?
> Este padrão de consulta tem frequentemente um grande número de subqueries, e resulta em topologia muito grande e complexa. O controlador do trabalho pode não ser capaz de lidar com uma topologia tão grande. Como regra geral, mantenha-se com menos de 40 inquilinos por 1 emprego na UE, e 60 inquilinos para 3 sU e 6 empregos da UE. Quando estiver a exceder a capacidade do controlador, o trabalho não começará com sucesso.



## <a name="get-help"></a>Obter ajuda
Para mais assistência, experimente o nosso [Microsoft Q&Uma página de perguntas para o Azure Stream Analytics](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

