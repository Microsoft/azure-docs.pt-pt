---
title: Monitorizar consultas
titleSuffix: Azure Cognitive Search
description: Monitorize as métricas de consulta para desempenho e entrada. Recolher e analisar as inputs de cadeias de consulta em registos de diagnóstico.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: 346a44f02667976d95125b72371b6e33715ee4b1
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211155"
---
# <a name="monitor-query-requests-in-azure-cognitive-search"></a>Monitorize pedidos de consulta em Pesquisa Cognitiva Azure

Este artigo explica como medir o desempenho e o volume da consulta usando métricas. Também explica como recolher os termos de entrada utilizados em consultas - informações necessárias quando precisa avaliar a utilidade e eficácia do seu corpus de pesquisa.

Os dados históricos que se alimentam de métricas são preservados durante 30 dias. Para uma retenção mais longa, ou para reportar dados operacionais e cordas de consulta, certifique-se de que permite uma [definição de diagnóstico](search-monitor-logs.md) que especifica uma opção de armazenamento.

As condições que maximizam a integridade das medições de dados incluem:

+ Utilize um serviço de faturação (um serviço criado no nível Básico ou Standard). O serviço gratuito é partilhado por vários subscritores, o que introduz uma certa volatilidade à medida que as cargas mudam.

+ Utilize uma única réplica, se possível, para que os cálculos se limitem a uma máquina. Se utilizar várias réplicas, as métricas de consulta são médias em vários nós, algumas das quais podem ser mais rápidas. Se estiver a afinar o desempenho da consulta, um único nó dá um ambiente mais estável para testes.

> [!Tip]
> Com código adicional do lado do cliente e Insights de Aplicação, também pode capturar cliques através de dados para uma visão mais profunda do que atrai o interesse dos seus utilizadores de aplicações. Para mais informações, consulte [a análise do tráfego de pesquisa.](search-traffic-analytics.md)

## <a name="query-volume-qps"></a>Volume de consulta (QPS)

O volume é medido como Consultas de **Pesquisa por Segundo** (QPS), uma métrica incorporada que pode ser reportada como uma média, contagem, valores mínimos ou máximos de consultas que executam dentro de uma janela de um minuto. Intervalos de um minuto (TimeGrain = "PT1M") para métricas é fixado dentro do sistema.

É comum as consultas executarem em milissegundos, por isso só as consultas que medem como segundos aparecerão nas métricas.

| Tipo de Agregação | Descrição |
|------------------|-------------|
| Média | O número médio de segundos dentro de um minuto durante o qual ocorreu a execução da consulta.|
| Contagem | O número de métricas emitidas para o registo dentro do intervalo de um minuto. |
| Máximo | O maior número de consultas de pesquisa por segundo registada durante um minuto. |
| Mínimo | O menor número de consultas de pesquisa por segundo registada durante um minuto.  |
| Soma | A soma de todas as consultas executadas dentro de um minuto.  |

Por exemplo, dentro de um minuto, poderá ter um padrão como este: um segundo de carga alta que é o máximo para SearchQueriesPerSecond, seguido de 58 segundos de carga média, e finalmente um segundo com apenas uma consulta, que é o mínimo.

Outro exemplo: se um nó emite 100 métricas, onde o valor de cada métrica é de 40, então "Conde" é 100, "Soma" é 4000, "Média" é de 40, e "Max" é 40.

## <a name="query-performance"></a>Desempenho de consultas

O desempenho da consulta em todo o serviço é medido como latência de pesquisa (quanto tempo uma consulta demora a completar) e consultas estranguladas que foram retiradas como resultado da contenção dos recursos.

### <a name="search-latency"></a>Pesquisar latência

| Tipo de Agregação | Latência | 
|------------------|---------|
| Média | Duração média da consulta em milissegundos. | 
| Contagem | O número de métricas emitidas para o registo dentro do intervalo de um minuto. |
| Máximo | A consulta de execução mais longa na amostra. | 
| Mínimo | A consulta de execução mais curta na amostra.  | 
| Total | Tempo total de execução de todas as consultas na amostra, executando dentro do intervalo (um minuto).  |

Considere o seguinte exemplo das métricas de **Latência** de Pesquisa: foram amostradas 86 consultas, com uma duração média de 23,26 milissegundos. Um mínimo de 0 indica que algumas consultas foram retiradas. A consulta de execução mais longa demorou 1000 milissegundos a ser concluída. O tempo total de execução foi de 2 segundos.

![Agregações de latência](./media/search-monitor-usage/metrics-latency.png "Agregações de latência")

### <a name="throttled-queries"></a>Consultas aceleradas

Consultas aceleradas referem-se a consultas que são retiradas em vez de processo. Na maioria dos casos, a estrangulamento é uma parte normal da gestão do serviço.  Não é necessariamente uma indicação de que há algo de errado.

O estrangulamento ocorre quando o número de pedidos atualmente processados excede os recursos disponíveis. Pode ver-se um aumento dos pedidos acelerados quando uma réplica é retirada da rotação ou durante a indexação. Tanto os pedidos de consulta como os pedidos de indexação são tratados pelo mesmo conjunto de recursos.

O serviço determina se deve retirar pedidos com base no consumo de recursos. A percentagem de recursos consumidos através da memória, CPU e IO do disco são médias ao longo de um período de tempo. Se esta percentagem exceder um limiar, todos os pedidos ao índice são acelerados até que o volume de pedidos seja reduzido. 

Dependendo do seu cliente, um pedido acelerado pode ser indicado desta forma:

+ Um serviço devolve um erro "Está a enviar muitos pedidos. Tente novamente mais tarde." 
+ Um serviço devolve um código de erro 503 indicando que o serviço não está disponível. 
+ Se estiver a utilizar o portal (por exemplo, Search Explorer), a consulta é deixada em silêncio e terá de clicar novamente em Search.

Para confirmar consultas aceleradas, utilize a métrica de consultas de **pesquisa throttled.** Pode explorar métricas no portal ou criar uma métrica de alerta, como descrito neste artigo. Para consultas que foram retiradas dentro do intervalo de amostragem, utilize a *Total* para obter a percentagem de consultas que não foram executadas.

| Tipo de Agregação | Limitação |
|------------------|-----------|
| Média | Percentagem de consultas caiu dentro do intervalo. |
| Contagem | O número de métricas emitidas para o registo dentro do intervalo de um minuto. |
| Máximo | Percentagem de consultas caiu dentro do intervalo.|
| Mínimo | Percentagem de consultas caiu dentro do intervalo. |
| Total | Percentagem de consultas caiu dentro do intervalo. |

Para consultas de **pesquisa aceleradas Percentagem**, mínimo, máximo, média e total, todas têm o mesmo valor: a percentagem de consultas de pesquisa que foram estranguladas, do número total de consultas de pesquisa durante um minuto.

Na seguinte imagem, o primeiro número é a contagem (ou número de métricas enviadas para o registo). As agregações adicionais, que aparecem no topo ou quando pairam sobre a métrica, incluem média, máxima e total. Nesta amostra, nenhum pedido foi retirado.

![Agregações aceleradas](./media/search-monitor-usage/metrics-throttle.png "Agregações aceleradas")

## <a name="explore-metrics-in-the-portal"></a>Explore as métricas no portal

Para uma rápida olhada nos números atuais, o separador **Monitorna** página de visão geral do serviço mostra três métricas (**Latência**de pesquisa, consultas de **pesquisa por segundo (por unidade**de pesquisa) , Intervalos de pesquisa **acelerados**medidos em horas, dias e semanas, com a opção de alterar o tipo de agregação.

Para uma exploração mais profunda, o explorador de métricas abertas do menu **monitor para** que possa colocar em camadas, ampliar e visualizar dados para explorar tendências ou anomalias. Saiba mais sobre o explorador de métricas completando este tutorial sobre a criação de um gráfico de [métricas.](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-metrics-explorer)

1. Na secção de Monitorização, selecione **Métricas** para abrir o explorador de métricas com o âmbito definido para o seu serviço de pesquisa.

1. Em Métrica, escolha um na lista de dropdown e reveja a lista de agregados disponíveis para um tipo preferido. A agregação define como os valores recolhidos serão amostrados em cada intervalo de tempo.

   ![Explorador de métricas para métrica de QPS](./media/search-monitor-usage/metrics-explorer-qps.png "Explorador de métricas para métrica de QPS")

1. No canto superior direito, dero o intervalo de tempo.

1. Escolha uma visualização. O padrão é um gráfico de linha.

1. Camada de agregações adicionais, escolhendo **adicionar métrica** e selecionando diferentes agregados.

1. Aproxime-se de uma área de interesse na tabela de linhas. Coloque o ponteiro do rato no início da área, clique e segure o botão do rato esquerdo, arraste para o outro lado da área e liberte o botão. O gráfico vai ampliar o intervalo de tempo.

## <a name="create-a-metric-alert"></a>Criar um alerta métrico

Um alerta métrico estabelece um limiar no qual receberá uma notificação ou desencadeará uma ação corretiva que definirá antecipadamente. 

Para um serviço de pesquisa, é comum criar um alerta métrico para a latência de pesquisa e consultas aceleradas. Se souber quando as consultas são retiradas, pode procurar remédios que reduzam a carga ou aumentem a capacidade. Por exemplo, se as consultas aceleradas aumentarem durante a indexação, pode adiá-la até que a atividade de consulta diminua.

Ao ultrapassar os limites de uma determinada configuração de divisória de réplica, a configuração de alertas para limiares de volume de consulta (QPS) também é útil.

1. Na secção de Monitorização, selecione **Alertas** e, em seguida, clique **+ Nova regra de alerta**. Certifique-se de que o seu serviço de pesquisa é selecionado como recurso.

1. Sob condição, clique em **Adicionar**.

1. Configure a lógica do sinal. Para o tipo de sinal, escolha **métricas** e, em seguida, selecione o sinal.

1. Depois de selecionar o sinal, pode utilizar um gráfico para visualizar dados históricos para uma decisão informada sobre como proceder com a configuração de condições.

1. Em seguida, desça para a lógica alerta. Para a prova de conceito, pode especificar um valor artificialmente baixo para fins de teste.

   ![Lógica de alerta](./media/search-monitor-usage/alert-logic-qps.png "Lógica de alerta")

1. Em seguida, especifique ou crie um Grupo de Ação. Esta é a resposta a invocar quando o limiar é cumprido. Pode ser uma notificação push ou uma resposta automatizada.

1. Por último, especifique os detalhes do Alerta. Nomeie e descreva o alerta, atribua um valor de gravidade e especifique se deve criar a regra num estado habilitado ou incapacitado.

   ![Detalhes de alerta](./media/search-monitor-usage/alert-details.png "Detalhes do alerta")

Se especificou uma notificação por e-mail, receberá um e-mail do "Microsoft Azure" com uma linha de assunto "Azure: Activated Severity: 3 `<your rule name>`".

## <a name="query-strings-used-in-queries"></a>Cordas de consulta usadas em consultas

Ao ativar o registo de diagnóstico, o sistema capta pedidos de consulta na tabela **AzureDiagnostics.** Como pré-requisito, já deve ter ativado o [registo de diagnóstico,](search-monitor-logs.md)especificando um espaço de trabalho de análise de registo ou outra opção de armazenamento.

1. Na secção de Monitorização, selecione **Registos** para abrir uma janela de consulta vazia no Log Analytics.

1. Executar a seguinte expressão para pesquisar Operações de Pesquisa, devolvendo um conjunto de resultados tabular constituído pelo nome da operação, cadeia de consulta, o índice consultado, e o número de documentos encontrados. As duas últimas declarações excluem cordas de consulta que consistem numa pesquisa vazia ou não especificada, sobre um índice de amostra, que reduz o ruído dos seus resultados.

   ```
    AzureDiagnostics 
     | project OperationName, Query_s, IndexName_s, Documents_d 
     | where OperationName == "Query.Search"
     | where Query_s != "?api-version=2019-05-06&search=*"
     | where IndexName_s != "realestate-us-sample-index"
   ```

1. Opcionalmente, coloque um filtro de Coluna na *Query_s* para pesquisar uma sintaxe ou corda específica. Por exemplo, pode filtrar por cima *é igual a* `?api-version=2019-05-06&search=*&%24filter=HotelName`).

   ![Cordas de consulta registadas](./media/search-monitor-usage/log-query-strings.png "Cordas de consulta registadas")

Enquanto esta técnica funciona para a investigação ad hoc, a construção de um relatório permite-lhe consolidar e apresentar as cordas de consulta num layout mais propício à análise.

## <a name="report-query-data"></a>Reportar dados de consulta

Power BI é uma ferramenta de reporte analítico que pode utilizar contra dados de registo armazenados no armazenamento blob ou num espaço de trabalho de Log Analytics.

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, reveja os fundamentos da monitorização do serviço de pesquisa para saber mais sobre toda a gama de capacidades de supervisão.

> [!div class="nextstepaction"]
> [Monitorizar operações e atividades em Pesquisa Cognitiva Azure](search-monitor-usage.md)