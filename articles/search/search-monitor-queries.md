---
title: Monitorar consultas
titleSuffix: Azure Cognitive Search
description: Monitorize as métricas de consulta para desempenho e produção. Recolher e analisar entradas de cadeias de consulta em registos de recursos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 169a90c12b30e0d083ce5c53ab7c6dd2495c4c23
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592395"
---
# <a name="monitor-query-requests-in-azure-cognitive-search"></a>Monitor solicita pedidos de consulta em Azure Cognitive Search

Este artigo explica como medir o desempenho e o volume de consulta usando métricas e registo de recursos. Também explica como recolher os termos de entrada utilizados em consultas - informações necessárias quando precisa de avaliar a utilidade e eficácia do seu corpus de pesquisa.

Os dados históricos que se alimentam em métricas são preservados por 30 dias. Para uma retenção mais longa, ou para reportar sobre dados operacionais e cadeias de consulta, certifique-se de que permite uma [definição de diagnóstico](search-monitor-logs.md) que especifica uma opção de armazenamento para eventos e métricas registados persistentes.

As condições que maximizam a integridade da medição de dados incluem:

+ Utilize um serviço fatural (um serviço criado no nível Básico ou Standard). O serviço gratuito é partilhado por vários subscritores, o que introduz uma certa volatilidade à medida que as cargas mudam.

+ Utilize uma única réplica e partição, se possível, para criar um ambiente contido e isolado. Se utilizar várias réplicas, as métricas de consulta são médias em vários nós, o que pode diminuir a precisão dos resultados. Da mesma forma, múltiplas divisórias significam que os dados são divididos, com o potencial de algumas divisórias poderem ter dados diferentes se a indexação também estiver em curso. Ao sintonizar o desempenho da consulta, um único nó e partição conferem um ambiente mais estável para testes.

> [!Tip]
> Com código adicional do lado do cliente e Insights de Aplicação, também pode capturar dados clicando através de dados para uma visão mais profunda do que atrai o interesse dos utilizadores da sua aplicação. Para obter mais informações, consulte [pesquisar a análise de tráfego.](search-traffic-analytics.md)

## <a name="query-volume-qps"></a>Volume de consulta (QPS)

O volume é medido como **Consultas de Pesquisa Por Segundo** (QPS), uma métrica incorporada que pode ser reportada como um valore médio, contagem, mínimo ou máximo de consultas que executam dentro de uma janela de um minuto. Os intervalos de um minuto (TimeGrain = "PT1M") para métricas são fixados dentro do sistema.

É comum que as consultas executem em milissegundos, por isso apenas as consultas que medem como segundos aparecerão em métricas.

| Tipo de Agregação | Description |
|------------------|-------------|
| Média | O número médio de segundos dentro de um minuto durante o qual ocorreu a execução de consultas.|
| de palavras | O número de métricas emitidas no registo dentro do intervalo de um minuto. |
| Máximo | O maior número de consultas de pesquisa por segundo registados durante um minuto. |
| Mínimo | O menor número de consultas de pesquisa por segundo registado durante um minuto.  |
| Soma | A soma de todas as consultas executadas dentro de um minuto.  |

Por exemplo, dentro de um minuto, pode ter um padrão como este: um segundo de carga alta que é o máximo para SearchQueriesPerSecond, seguido de 58 segundos de carga média, e finalmente um segundo com apenas uma consulta, que é o mínimo.

Outro exemplo: se um nó emite 100 métricas, onde o valor de cada métrica é 40, então "Count" é 100, "Sum" é 4000, "Média" é 40, e "Max" é 40.

## <a name="query-performance"></a>Desempenho de consultas

Em todo o serviço, o desempenho da consulta é medido como latência de pesquisa (quanto tempo uma consulta leva para completar) e consultas estranguladas que foram largadas como resultado da contenção de recursos.

### <a name="search-latency"></a>Procurar latência

| Tipo de Agregação | Latência | 
|------------------|---------|
| Média | Duração média da consulta em milissegundos. | 
| de palavras | O número de métricas emitidas no registo dentro do intervalo de um minuto. |
| Máximo | Consulta de maior duração na amostra. | 
| Mínimo | Consulta de funcionamento mais curta na amostra.  | 
| Total | Tempo total de execução de todas as consultas na amostra, executando dentro do intervalo (um minuto).  |

Considere o seguinte exemplo das métricas de **Procura de Latência:** foram amostradas 86 consultas, com uma duração média de 23,26 milissegundos. Um mínimo de 0 indica que algumas consultas foram retiradas. A consulta mais longa demorou 1000 milissegundos para ser concluída. O tempo total de execução foi de 2 segundos.

![Agregações de latência](./media/search-monitor-usage/metrics-latency.png "Agregações de latência")

### <a name="throttled-queries"></a>Consultas estranguladas

Consultas estranguladas referem-se a consultas que são abandonadas em vez de processar. Na maioria dos casos, o estrangulamento é uma parte normal da execução do serviço.  Não é necessariamente uma indicação de que há algo de errado.

O estrangulamento ocorre quando o número de pedidos atualmente processados excede os recursos disponíveis. Pode ver-se um aumento dos pedidos acelerados quando uma réplica é retirada da rotação ou durante a indexação. Tanto os pedidos de consulta como de indexação são tratados pelo mesmo conjunto de recursos.

O serviço determina se deve deixar cair os pedidos com base no consumo de recursos. A percentagem de recursos consumidos através da memória, CPU e IO do disco são mediados durante um período de tempo. Se esta percentagem exceder um limiar, todos os pedidos ao índice são acelerados até que o volume de pedidos seja reduzido. 

Dependendo do seu cliente, um pedido de aceleração pode ser indicado desta forma:

+ Um serviço retorna um erro "Está a enviar demasiados pedidos. Tente novamente mais tarde." 
+ Um serviço devolve um código de erro 503 indicando que o serviço não está disponível atualmente. 
+ Se estiver a utilizar o portal (por exemplo, Search Explorer), a consulta é deixada silenciosamente e terá de clicar novamente em Procurar.

Para confirmar consultas estranguladas, use a métrica **das consultas de pesquisa throttled.** Pode explorar métricas no portal ou criar uma métrica de alerta como descrito neste artigo. Para consultas que foram deixadas dentro do intervalo de amostragem, utilize a *Total* para obter a percentagem de consultas que não executaram.

| Tipo de Agregação | Limitação |
|------------------|-----------|
| Média | Percentagem de consultas diminuiu dentro do intervalo. |
| de palavras | O número de métricas emitidas no registo dentro do intervalo de um minuto. |
| Máximo | Percentagem de consultas diminuiu dentro do intervalo.|
| Mínimo | Percentagem de consultas diminuiu dentro do intervalo. |
| Total | Percentagem de consultas diminuiu dentro do intervalo. |

Para **a Percentagem de Consultas de Pesquisa Throttled**, mínima, máxima, média e total, todas têm o mesmo valor: a percentagem de consultas de pesquisa que foram estrangulada, do número total de consultas de pesquisa durante um minuto.

Na imagem seguinte, o primeiro número é a contagem (ou número de métricas enviadas para o registo). As agregações adicionais, que aparecem no topo ou quando pairam sobre a métrica, incluem a média, máxima e total. Nesta amostra, nenhum pedido foi retirado.

![Agregações aceleradas](./media/search-monitor-usage/metrics-throttle.png "Agregações aceleradas")

## <a name="explore-metrics-in-the-portal"></a>Explore métricas no portal

Para uma rápida análise dos números atuais, o **separador monitor na** página de visão geral do serviço mostra três métricas (**Procura de latência,** **consultas de pesquisa por segundo (por unidade de pesquisa)**, **Percentagens de pesquisa aceleradas**) sobre intervalos fixos medidos em horas, dias e semanas, com a opção de alterar o tipo de agregação.

Para uma exploração mais profunda, o explorador de métricas abertas a partir do menu **de Monitorização** para que possa em camadas, zoom e visualizar dados para explorar tendências ou anomalias. Saiba mais sobre o explorador de métricas completando este [tutorial na criação de um gráfico de métricas.](../azure-monitor/essentials/tutorial-metrics-explorer.md)

1. Na secção de Monitorização, selecione **Métricas** para abrir o explorador de métricas com o âmbito definido para o seu serviço de pesquisa.

1. Em Métrica, escolha uma da lista de abandono e reveja a lista de agregações disponíveis para um tipo preferencial. A agregação define como os valores recolhidos serão amostrados ao longo de cada intervalo de tempo.

   ![Explorador de métricas para métrica QPS](./media/search-monitor-usage/metrics-explorer-qps.png "Explorador de métricas para métrica QPS")

1. No canto superior direito, desa um intervalo de tempo.

1. Escolha uma visualização. O padrão é um gráfico de linha.

1. Camada agregações adicionais escolhendo **adicionar métrica** e selecionando diferentes agregações.

1. Aproxime-se numa área de interesse na tabela de linhas. Coloque o ponteiro do rato no início da área, clique e segure o botão do rato esquerdo, arraste para o outro lado da área e solte o botão. O gráfico vai ampliar o intervalo de tempo.

## <a name="identify-strings-used-in-queries"></a>Identificar cordas usadas em consultas

Quando ativa a marcação de recursos, o sistema captura pedidos de consulta na tabela **AzureDiagnostics.** Como pré-requisito, já deve ter ativado [a sessão de registo de recursos,](search-monitor-logs.md)especificando um espaço de trabalho de análise de registo ou outra opção de armazenamento.

1. Na secção de Monitorização, selecione **Logs** para abrir uma janela de consulta vazia no Log Analytics.

1. Executar a seguinte expressão para pesquisar operações de pesquisa.Pesquisar, devolvendo um conjunto de resultados tabulares constituído pelo nome de operação, cadeia de consulta, o índice consultado e o número de documentos encontrados. As duas últimas declarações excluem as cadeias de consulta que consistem numa pesquisa vazia ou não especificada, sobre um índice de amostra, que reduz o ruído nos seus resultados.

   ```
   AzureDiagnostics
   | project OperationName, Query_s, IndexName_s, Documents_d
   | where OperationName == "Query.Search"
   | where Query_s != "?api-version=2020-06-30&search=*"
   | where IndexName_s != "realestate-us-sample-index"
   ```

1. Opcionalmente, coloque um filtro de coluna no *Query_s* para procurar por uma sintaxe ou cadeia específica. Por exemplo, pode filtrar-se *é igual a* `?api-version=2020-06-30&search=*&%24filter=HotelName` ).

   ![Cadeias de consulta registadas](./media/search-monitor-usage/log-query-strings.png "Cadeias de consulta registadas")

Enquanto esta técnica trabalha para a investigação ad hoc, a construção de um relatório permite consolidar e apresentar as cordas de consulta num layout mais propício à análise.

## <a name="identify-long-running-queries"></a>Identificar consultas de longa duração

Adicione a coluna de duração para obter os números para todas as consultas, e não apenas aquelas que são recolhidas como uma métrica. A triagem destes dados mostra quais as consultas que demoram mais tempo a ser completadas.

1. Na secção de Monitorização, selecione **Logs** para consultar informações de registo.

1. Executar a seguinte consulta para devolver consultas, ordenadas por duração em milissegundos. As consultas mais longas estão no topo.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search"
   | sort by DurationMs
   ```

   ![Ordenar consultas por duração](./media/search-monitor-usage/azurediagnostics-table-sortby-duration.png "Ordenar consultas por duração")

## <a name="create-a-metric-alert"></a>Criar um alerta métrico

Um alerta métrico estabelece um limiar no qual receberá uma notificação ou desencadeará uma ação corretiva que definir antecipadamente. 

Para um serviço de pesquisa, é comum criar um alerta métrico para a latência da pesquisa e consultas estranguladas. Se souber quando as consultas são retiradas, pode procurar remédios que reduzam a carga ou aumentem a capacidade. Por exemplo, se as consultas aceleradas aumentarem durante a indexação, pode adiá-la até que a atividade de consulta diminua.

Ao ultrapassar os limites de uma determinada configuração de partição de réplicas, a criação de alertas para os limiares de volume de consulta (QPS) também é útil.

1. Na secção de Monitorização, selecione **Alertas** e, em seguida, clique **em + Nova regra de alerta**. Certifique-se de que o seu serviço de pesquisa é selecionado como recurso.

1. Em Condições, clique **em Adicionar**.

1. Configure a lógica do sinal. Para o tipo de sinal, escolha **métricas** e, em seguida, selecione o sinal.

1. Depois de selecionar o sinal, pode utilizar um gráfico para visualizar dados históricos para uma decisão informada sobre como proceder com a configuração das condições.

1. Em seguida, desça para a lógica de alerta. Para a prova de conceito, pode especificar um valor artificialmente baixo para fins de teste.

   ![Lógica de alerta](./media/search-monitor-usage/alert-logic-qps.png "Lógica de alerta")

1. Em seguida, especifique ou crie um Grupo de Ação. Esta é a resposta a invocar quando o limiar for atingido. Pode ser uma notificação push ou uma resposta automatizada.

1. Por último, especifique os detalhes do alerta. Nomeie e descreva o alerta, atribua um valor de gravidade e especifique se deve criar a regra num estado ativado ou desativado.

   ![Detalhes do alerta](./media/search-monitor-usage/alert-details.png "Detalhes do alerta")

Se especificar uma notificação por e-mail, receberá um e-mail do "Microsoft Azure" com uma linha de assunto de "Azure: Severidade Ativada: 3 `<your rule name>` ".

<!-- ## Report query data

Power BI is an analytical reporting tool useful for visualizing data, including log information. If you are collecting data in Blob storage, a Power BI template makes it easy to spot anomalies or trends. Use this link to download the template. -->

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, reveja os fundamentos da monitorização do serviço de pesquisa para saber mais sobre toda a gama de capacidades de supervisão.

> [!div class="nextstepaction"]
> [Monitorizar operações e atividades em Azure Cognitive Search](search-monitor-usage.md)