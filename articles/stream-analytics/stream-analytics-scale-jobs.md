---
title: Escalar verticalmente e horizontalmente em trabalhos de Azure Stream Analytics
description: Este artigo descreve como dimensionar um trabalho de Stream Analytics Particionando dados de entrada, ajustando a consulta e definindo unidades de streaming de trabalho.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: 4f89fb07fbbff3beee66f80675bb5c3a32136807
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458755"
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>Dimensionar um trabalho de Azure Stream Analytics para aumentar a taxa de transferência
Este artigo mostra como ajustar uma consulta de Stream Analytics para aumentar a taxa de transferência para trabalhos do Stream Analytics. Você pode usar o seguinte guia para dimensionar seu trabalho para lidar com a carga mais alta e aproveitar mais recursos do sistema (como mais largura de banda, mais recursos de CPU, mais memória).
Como pré-requisito, talvez seja necessário ler os seguintes artigos:
-   [Compreender e ajustar as Unidades de Transmissão em fluxo](stream-analytics-streaming-unit-consumption.md)
-   [Criar trabalhos do paralelizáveis](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Caso 1 – sua consulta é inerentemente totalmente paralelizáveis entre partições de entrada
Se sua consulta for totalmente paralelizáveis entre partições de entrada, você poderá seguir as seguintes etapas:
1.  Crie sua consulta para ser embaraçosamente paralela usando a palavra-chave **Partition by** . Veja mais detalhes na seção trabalhos paralelos do embaraçosamente [nesta página](stream-analytics-parallelization.md).
2.  Dependendo dos tipos de saída usados em sua consulta, alguma saída pode não ser paralelizáveis ou precisar de mais configuração para ser embaraçosamente paralela. Por exemplo, as saídas SQL, SQL DW e PowerBI não são paralelizáveis. As saídas são sempre mescladas antes de enviar para o coletor de saída. BLOBs, tabelas, ADLS, barramento de serviço e função do Azure são paralelizados automaticamente. O CosmosDB e o Hub de eventos precisam ter a configuração PartitionKey definida para corresponder ao campo **Partition by** (geralmente PartitionID). Para o Hub de eventos, também preste atenção extra para fazer a correspondência entre o número de partições de todas as entradas e todas as saídas para evitar entre partições. 
3.  Execute sua consulta com **6 Su** (que é a capacidade total de um único nó de computação) para medir a taxa de transferência máxima atingível e, se você estiver usando **Group by**, meça quantos grupos (cardinalidade) o trabalho pode manipular. Os sintomas gerais do trabalho que está atingindo os limites de recursos do sistema são os seguintes.
    - A métrica de utilização de% de SU está acima de 80%. Isso indica que o uso de memória é alto. Os fatores que contribuem para o aumento dessa métrica são descritos [aqui](stream-analytics-streaming-unit-consumption.md). 
    -   O carimbo de data/hora de saída está atrasado em relação ao tempo do relógio de parede. Dependendo da lógica de consulta, o carimbo de data/hora de saída pode ter um deslocamento de lógica do tempo do relógio de parede. No entanto, eles devem progredir quase na mesma taxa. Se o carimbo de data/hora de saída estiver prestes a ficar mais atrás, é um indicador de que o sistema está sobrecorrendo. Pode ser resultado da limitação do coletor de saída downstream ou da alta utilização da CPU. Não fornecemos a métrica de utilização da CPU no momento, portanto, pode ser difícil diferenciar as duas.
        - Se o problema for devido à limitação do coletor, talvez seja necessário aumentar o número de partições de saída (e também as partições de entrada para manter o trabalho totalmente paralelizáveis) ou aumentar a quantidade de recursos do coletor (por exemplo, o número de unidades de solicitação para CosmosDB).
    - No diagrama de trabalho, há uma métrica de evento de pendência por partição para cada entrada. Se a métrica de evento de pendências continuar aumentando, também será um indicador de que o recurso do sistema está restrito (seja devido à limitação do coletor de saída ou à alta CPU).
4.  Depois de determinar os limites do que um trabalho com 6 SU pode alcançar, você pode extrapolar linearmente a capacidade de processamento do trabalho à medida que adiciona mais SUs, supondo que você não tenha qualquer distorção de dados que torne determinada partição "quente".

> [!NOTE]
> Escolha o número certo de unidades de streaming: como Stream Analytics cria um nó de processamento para cada 6 SU adicionado, é melhor tornar o número de nós um divisor do número de partições de entrada, para que as partições possam ser distribuídas uniformemente entre os nós.
> Por exemplo, você mediu que seu trabalho de 6 SU pode atingir uma taxa de processamento de 4 MB/s e sua contagem de partição de entrada é 4. Você pode optar por executar seu trabalho com 12 SU para obter a taxa de processamento de aproximadamente 8 MB/s ou 24 SU para atingir 16 MB/s. Em seguida, você pode decidir quando aumentar o número SU do trabalho para o valor, como uma função de sua taxa de entrada.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Caso 2 – se sua consulta não for embaraçosamente Parallel.
Se sua consulta não for embaraçosamente paralela, você poderá seguir as etapas a seguir.
1.  Comece com uma consulta sem **partição** primeiro para evitar a complexidade do particionamento e execute sua consulta com 6 su para medir a carga máxima como no [caso 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  Se você puder obter a carga antecipada em termos de taxa de transferência, pronto. Como alternativa, você pode optar por medir o mesmo trabalho em execução em 3 SU e 1 SU para descobrir o número mínimo de SU que funciona para seu cenário.
3.  Se você não conseguir obter a taxa de transferência desejada, tente dividir a consulta em várias etapas, se possível, se ela ainda não tiver várias etapas e alocar até 6 SU para cada etapa na consulta. Por exemplo, se você tiver 3 etapas, aloque 18 SU na opção "dimensionar".
4.  Ao executar esse trabalho, Stream Analytics coloca cada etapa em seu próprio nó com os 6 recursos de SU dedicados. 
5.  Se ainda não tiver obtido o destino de carga, você poderá tentar usar a **partição** começando com as etapas mais próximas da entrada. Para o operador **Group by** que pode não ser naturalmente particionável, você pode usar o padrão agregado local/global para executar um **grupo** particionado seguido por um grupo não particionado **por**. Por exemplo, se você quiser contar quantos carros passam por cada estande de chamada a cada 3 minutos, e o volume dos dados está além do que pode ser tratado por 6 SU.

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
Na consulta acima, você está contando carros por estande de chamada por partição e, em seguida, adicionando a contagem de todas as partições juntas.

Depois de particionado, para cada partição da etapa, aloque até 6 SU, cada partição com 6 SU é o máximo, portanto, cada partição pode ser colocada em seu próprio nó de processamento.

> [!Note]
> Se a consulta não puder ser particionada, adicionar SU adicionais em uma consulta de várias etapas nem sempre poderá melhorar a taxa de transferência. Uma maneira de obter o desempenho é reduzir o volume nas etapas iniciais usando o padrão agregado local/global, conforme descrito acima na etapa 5.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Caso 3-você está executando muitas consultas independentes em um trabalho.
Para determinados casos de uso de ISVs, em que é mais econômico processar dados de vários locatários em um único trabalho, usando entradas e saídas separadas para cada locatário, você pode acabar executando algumas (por exemplo, 20) consultas independentes em um único trabalho. A suposição é que a carga de tal subconsulta seja relativamente pequena. Nesse caso, você pode seguir as etapas a seguir.
1.  Nesse caso, não use **Partition by** na consulta
2.  Reduza a contagem de partições de entrada para o menor valor possível de 2 se você estiver usando o Hub de eventos.
3.  Execute a consulta com 6 SU. Com a carga esperada para cada subconsulta, adicione tantas subconsultas quanto possível, até que o trabalho esteja atingindo os limites de recursos do sistema. Consulte o [caso 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) para os sintomas quando isso acontece.
4.  Quando você estiver atingindo o limite de subconsulta medido acima, comece a adicionar a subconsulta a um novo trabalho. O número de trabalhos a serem executados como uma função do número de consultas independentes deve ser razoavelmente linear, supondo que você não tenha nenhuma distorção de carga. Em seguida, você pode prever quantos trabalhos de 6 SU precisam ser executados como uma função do número de locatários que você gostaria de fornecer.
5.  Ao usar a junção de dados de referência com essas consultas, junte as entradas antes de ingressar com os mesmos dados de referência. Em seguida, divida os eventos, se necessário. Caso contrário, cada junção de dados de referência mantém uma cópia dos dados de referência na memória, provavelmente esgotando o uso da memória desnecessariamente.

> [!Note] 
> Quantos locatários colocar em cada trabalho?
> Esse padrão de consulta geralmente tem um grande número de subconsultas e resulta em topologia muito grande e complexa. O controlador do trabalho pode não ser capaz de lidar com uma topologia tão grande. Como regra geral, mantenha-se sob 40 locatários para um trabalho SU e 60 locatários para trabalhos de 3 SU e 6 SU. Quando você estiver excedendo a capacidade do controlador, o trabalho não será iniciado com êxito.



## <a name="get-help"></a>Obter ajuda
Para obter assistência, tente nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

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

