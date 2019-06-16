---
title: Aumentar verticalmente e horizontalmente tarefas do Azure Stream Analytics
description: Este artigo descreve como reduzir horizontalmente uma tarefa do Stream Analytics para a criação de partições de dados de entrada, ajuste a consulta e definir as unidades de transmissão em fluxo de trabalho.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: f4307da2e74846507cafb9f767a6ccae855e42a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60816801"
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>Dimensionar uma tarefa Azure Stream Analytics para aumentar o débito
Este artigo mostra-lhe como otimizar uma consulta do Stream Analytics para aumentar o débito para tarefas do Stream Analytics. Pode utilizar o guia seguinte para dimensionar o seu trabalho para processar carga superior e tirar partido de mais recursos do sistema (por exemplo, mais largura de banda, mais recursos de CPU, mais memória).
Como pré-requisito, poderá ter de ler os artigos seguintes:
-   [Compreender e ajustar as Unidades de Transmissão em fluxo](stream-analytics-streaming-unit-consumption.md)
-   [Criar tarefas de ponto pode ser paralelizadas](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Caso 1 – sua consulta é inerentemente totalmente ponto pode ser paralelizado entre partições de entrada
Se a consulta for inerentemente totalmente ponto pode ser paralelizada entre partições de entrada, pode seguir os passos seguintes:
1.  Conceber a sua consulta para ser constrangedoramente paralelas com o uso **PARTITION BY** palavra-chave. Ver mais detalhes na secção tarefas Constrangedoramente paralelas [nesta página](stream-analytics-parallelization.md).
2.  Consoante os tipos de saída utilizados na sua consulta, alguns de saída pode ou não pode estar ponto pode ser paralelizada, ou precisar de mais configuração seja constrangedoramente paralelas. Por exemplo, as saídas SQL, o armazém de dados SQL e o Power BI não são ponto pode ser paralelizadas. Saídas sempre sejam intercaladas antes de enviar para o sink de saída. BLOBs, tabelas, ADLS, do Service Bus e função do Azure são automaticamente paralelizada. Cosmos DB e o Hub de eventos tem de ter a configuração de PartitionKey definida para corresponder com o **PARTITION BY** campo (normalmente, PartitionId). Para o Hub de eventos, também preste especial atenção para corresponder ao número de partições para todas as entradas e saídas de todos os para evitar a ativação pós-falha entre partições. 
3.  Executar a sua consulta **6 SU** (que é a capacidade total de um único nó de computação) para medir o débito alcançável máximo, e se estiver a utilizar **GROUP BY**, medir o número de grupos (cardinalidade) a tarefa podem Identificador. Sintomas gerais da tarefa a atingir os limites de recursos do sistema são as seguintes.
    - Métrica % utilization de SU é mais de 80%. Isto indica a memória, a utilização é elevada. Os fatores que contribuem para o aumento desta métrica são descritos [aqui](stream-analytics-streaming-unit-consumption.md). 
    -   Timestamp de saída é atrasado em relação ao tempo de relógio. Dependendo de sua lógica de consulta, o carimbo de hora de saída pode ter um desvio de lógica a partir da hora de relógio de parede. No entanto, deve avançar aproximadamente na mesma taxa. Se o carimbo de hora de saída recai mais e mais atrás, é um indicador de que o sistema é overworking. Ele pode ser o resultado da saída downstream sink limitação ou elevada utilização da CPU. Não fornecemos métrica de utilização de CPU neste momento, pelo que pode ser difícil diferenciar os dois.
        - Se o problema é devido à limitação de sink, poderá ter de aumentar o número de partições de saída (e também as partições para manter a tarefa de ponto pode ser paralelizada totalmente de entrada), ou aumente a quantidade de recursos do coletor (por exemplo o número de unidades de pedido para o cosmos DB).
    - No diagrama de tarefas, há um por métrica de eventos de registo de segurança de partição para cada entrada. Se a aumentar a métrica de eventos do registo de segurança, também é um indicador de que o recurso do sistema é restrito (seja devido à limitação de sink de saída ou elevada da CPU).
4.  Depois de determinar os limites do que uma tarefa SU 6 pode entrar, pode extrapolar linearmente a capacidade de processamento da tarefa à medida que adiciona SUs mais, partindo do princípio de que não tem quaisquer dados inclinar que faz com que determinados partição "quentes".

> [!NOTE]
> Escolha o número certo de unidades de transmissão em fluxo: Porque o Stream Analytics cria um nó de processamento para cada SU 6 adicionado, é melhor tornar o número de nós um divisor do número de partições de entrada, para que as partições podem ser distribuídas uniformemente em todos os nós.
> Por exemplo, ter medido o 6 tarefa SU pode obter 4 MB/s, processamento de taxa e sua contagem de partições de entrada é 4. Pode optar por executar o seu trabalho com 12 SU para alcançar a taxa de processamento de aproximadamente 8 MB/s ou 24 SU para alcançar 16 MB/s. Em seguida, pode decidir quando aumentar o número SU para o trabalho para o qual o valor, como uma função de sua taxa de entrada.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Caso 2 - se a consulta não for constrangedoramente paralela.
Se a consulta não for constrangedoramente paralela, pode seguir os passos seguintes.
1.  Começar com uma consulta sem qualquer **PARTITION BY** primeiro para evitar a complexidade de criação de partições e executar a consulta com 6 SU para medir a carga máxima, como mostrado na [caso 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  Se é possível obter a sua carga antecipada na condição de taxa de transferência, isso é tudo. Em alternativa, pode optar por medir a mesma tarefa em execução em 3 SU e 1 SU, para descobrir o número mínimo de SU que funciona para o seu cenário.
3.  Se não é possível alcançar o rendimento desejado, tente interromper a sua consulta em várias etapas se possível, se não tiver vários passos e alocar SU até 6 para cada passo na consulta. Por exemplo, se tiver 3 passos, alocar SU 18 na opção "Dimensionar".
4.  Ao executar um trabalho, o Stream Analytics coloca cada passo no seu próprio nó com recursos dedicados de SU 6. 
5.  Se ainda não tiver obtido o destino de carregamento, pode tentar usar **PARTITION BY** a partir de mais perto dos passos para a entrada. Para **GROUP BY** operador que não pode estar naturalmente partições, pode utilizar o padrão de agregação local/global para executar um particionada **GROUP BY** seguido de um não-particionada **GROUP BY** . Por exemplo, se quiser contar quantas carros percorrer cada pedágio a cada 3 minutos e o volume dos dados é além do que pode ser manipulado por 6 SU.

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
Na consulta acima, é Contagem carros por pedágio por partição e, em seguida, em conjunto, adicionando a contagem de todas as partições.

Depois de particionada, para cada partição do passo, alocar SU até 6, cada partição ter 6 SU é o máximo, pelo que cada partição pode ser colocada em seu próprio nó de processamento.

> [!Note]
> Se a sua consulta não pode ser particionada, adicionar SU adicional numa consulta de vários passos pode não sempre melhorar o débito. Uma forma de obter o desempenho é reduzir o volume nas etapas iniciais com o padrão de agregação local/global, conforme descrito acima, no passo 5.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Caso 3 - estiver a executar muitas consultas independentes numa tarefa.
Para casos, onde é mais rentável para processar dados de vários inquilinos numa única tarefa, de utilização de determinadas ISV com separado entradas e saídas para cada inquilino, poderá acabar alguns (por exemplo, 20) a executar consultas independentes numa única tarefa. A pressuposição é a que carga de cada tais subconsulta é relativamente pequena. Neste caso, pode seguir os passos seguintes.
1.  Neste caso, não utilize **PARTITION BY** na consulta
2.  Reduza o número de partições de entrada para o valor mais baixo possível de 2, se estiver a utilizar o Hub de eventos.
3.  Execute a consulta com 6 SU. Com a carga esperada para cada subconsulta, adicione tantos tais subconsultas possível, até que a tarefa prestes a atingir os limites de recursos de sistema. Consulte a [caso 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) para os sintomas quando isso acontece.
4.  Uma vez que está a atingir o limite de subconsulta medido acima, começar a adicionar a subconsulta para uma nova tarefa. O número de tarefas para ser executado como uma função do número de consultas independentes deve ser bastante linear, partindo do princípio de que não tem qualquer carga skew. Em seguida, pode prever quantas tarefas SU 6 tem de ser executado como uma função do número de inquilinos para servir.
5.  Ao utilizar a associação de dados de referência com estas consultas, union as entradas em conjunto, antes de se juntar com os mesmos dados de referência. Em seguida, divida os eventos, se necessário. Caso contrário, cada associação de dados de referência mantém uma cópia dos dados de referência na memória, provavelmente desse desnecessariamente o uso de memória.

> [!Note] 
> O número de inquilinos para colocar em cada tarefa?
> Este padrão de consulta, muitas vezes, tem um grande número de subconsultas e resulta numa topologia muito grande e complexa. O controlador da tarefa não pode ser capaz de lidar com tal uma topologia grandes. Como regra prática, mantenha-se em 40 inquilinos para 1 tarefa SU e 60 inquilinos para 3 SU e 6 tarefas SU. Quando estão a exceder a capacidade do controlador, a tarefa não será iniciado com êxito.



## <a name="get-help"></a>Obter ajuda
Para obter assistência, tente nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos Seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
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

