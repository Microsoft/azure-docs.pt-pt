---
title: Padrões da solução Azure Stream Analytics
description: Conheça padrões de solução comuns para o Azure Stream Analytics, tais como dashboarding, mensagens de eventos, lojas de dados, enriquecimento de dados de referência e monitorização.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 3b95863c1ae53bd0642aec356f55aba1faf8ef09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535787"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Padrões da solução Azure Stream Analytics

Como muitos outros serviços em Azure, o Stream Analytics é melhor utilizado com outros serviços para criar uma solução de ponta a ponta maior. Este artigo discute soluções simples do Azure Stream Analytics e vários padrões arquitetónicos. Você pode basear-se nestes padrões para desenvolver soluções mais complexas. Os padrões descritos neste artigo podem ser usados em uma grande variedade de cenários. Exemplos de padrões específicos de cenário estão disponíveis em arquiteturas de [soluções Azure.](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics)

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>Criar um trabalho de Stream Analytics para alimentar experiência de dashboarding em tempo real

Com o Azure Stream Analytics, pode levantar-se rapidamente em dashboards e alertas em tempo real. Uma solução simples ingere eventos a partir de Event Hubs ou IoT Hub, e [alimenta o dashboard Power BI com um conjunto](/power-bi/service-real-time-streaming)de dados de streaming . Para mais informações, consulte os dados de chamadas telefónicas detalhados [analise os dados de chamadas telefónicas com o Stream Analytics e visualize os resultados no painel power BI](stream-analytics-manage-job.md).

![Painel de instrumentos ASA Power BI](media/stream-analytics-solution-patterns/pbidashboard.png)

Esta solução pode ser construída em poucos minutos do portal Azure. Não há codificação extensiva envolvida, e a linguagem SQL é usada para expressar a lógica do negócio.

Este padrão de solução oferece a menor latência desde a fonte do evento até ao dashboard Power BI num browser. O Azure Stream Analytics é o único serviço Azure com esta capacidade incorporada.

## <a name="use-sql-for-dashboard"></a>Utilize o SQL para o painel de instrumentos

O painel power bi oferece baixa latência, mas não pode ser usado para produzir relatórios power bi completos. Um padrão comum de reporte é a de saída dos seus dados para uma base de dados SQL primeiro. Em seguida, utilize o conector SQL do Power BI para consultar o SQL para obter os dados mais recentes.

![Painel de instrumentos ASA SQL](media/stream-analytics-solution-patterns/sqldashboard.png)

A utilização da base de dados SQL dá-lhe mais flexibilidade, mas à custa de uma latência ligeiramente superior. Esta solução é ideal para empregos com requisitos de latência superiores a um segundo. Com este método, pode maximizar as capacidades do Power BI para cortar e picar os dados para relatórios, e muito mais opções de visualização. Também ganha a flexibilidade de utilizar outras soluções de tablier, como o Tableau.

O SQL não é uma loja de dados de alta produção. A entrada máxima para uma base de dados SQL do Azure Stream Analytics ronda atualmente os 24 MB/s. Se as fontes de evento na sua solução produzirem dados a um ritmo mais elevado, é necessário utilizar a lógica de processamento no Stream Analytics para reduzir a taxa de saída para SQL. Técnicas como filtragem, agregados com janelas, padrão correspondente sonantes e funções analíticas podem ser usadas. A taxa de saída para o SQL pode ser otimizada utilizando técnicas descritas na saída do Azure Stream Analytics para a Base de [Dados Azure SQL](stream-analytics-sql-output-perf.md).

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Incorpore insights em tempo real na sua aplicação com mensagens de eventos

O segundo uso mais popular do Stream Analytics é gerar alertas em tempo real. Neste padrão de solução, a lógica empresarial no Stream Analytics pode ser usada para detetar [padrões ou anomalias temporais e espaciais,](stream-analytics-geospatial-functions.md) produzindo depois sinais de alerta. [anomalies](stream-analytics-machine-learning-anomaly-detection.md) No entanto, ao contrário da solução do dashboard onde o Stream Analytics utiliza o Power BI como ponto final preferido, podem ser utilizados vários sumidouros intermédios. Estes lavatórios incluem Hubs de Eventos, Ônibus de Serviço e Funções Azure. Você, como construtor de aplicações, precisa decidir quais os dados que afundam melhor para o seu cenário.

A lógica do consumidor de eventos a jusante deve ser implementada para gerar alertas no seu fluxo de trabalho de negócioexistente. Como pode implementar uma lógica personalizada nas Funções Azure, o Azure Functions é a forma mais rápida de realizar esta integração. Um tutorial para usar a Função Azure como saída para um trabalho de Stream Analytics pode ser encontrado em [Funções Run Azure a partir de trabalhos azure Stream Analytics](stream-analytics-with-azure-functions.md). A Azure Functions também suporta vários tipos de notificações, incluindo texto e e-mail. A Aplicação Lógica também pode ser usada para tal integração, com Hubs de Eventos entre stream analytics e Logic App.

![App de mensagens de eventos ASA](media/stream-analytics-solution-patterns/eventmessagingapp.png)

O Event Hubs, por outro lado, oferece o ponto de integração mais flexível. Muitos outros serviços, como o Azure Data Explorer e o Time Series Insights podem consumir eventos a partir de Centros de Eventos. Os serviços podem ser ligados diretamente ao Afundado de Hubs de Eventos do Azure Stream Analytics para completar a solução. O Event Hubs é também o corretor de mensagens de maior produção disponível no Azure para tais cenários de integração.

## <a name="dynamic-applications-and-websites"></a>Aplicações e websites dinâmicos

Pode criar visualizações personalizadas em tempo real, como dashboard ou visualização de mapas, utilizando o Azure Stream Analytics e o Serviço De Sinalização Azure. Utilizando o SignalR, os clientes web podem ser atualizados e mostrar conteúdo dinâmico em tempo real.

![App dinâmica ASA](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Incorpore insights em tempo real na sua aplicação através de lojas de dados

A maioria dos serviços web e aplicações web hoje em dia usam um padrão de pedido/resposta para servir a camada de apresentação. O padrão de pedido/resposta é simples de construir e pode ser facilmente dimensionado com tempo de resposta baixo usando uma fachada apátrida e lojas escaláveis, como cosmos DB.

O elevado volume de dados cria frequentemente estrangulamentos de desempenho num sistema baseado em CRUD. O padrão de solução de fornecimento de [eventos](/azure/architecture/patterns/event-sourcing) é usado para resolver os estrangulamentos de desempenho. Padrões temporais e insights também são difíceis e ineficientes de extrair de uma loja de dados tradicional. Aplicações modernas baseadas em dados de alto volume muitas vezes adotam uma arquitetura baseada em fluxo de dados. O Azure Stream Analytics como motor de computação para dados em movimento é um eixo nessa arquitetura.

![App de sourcing de eventos ASA](media/stream-analytics-solution-patterns/eventsourcingapp.png)

Neste padrão de solução, os eventos são processados e agregados em lojas de dados pelo Azure Stream Analytics. A camada de aplicação interage com as lojas de dados utilizando o padrão tradicional de pedido/resposta. Devido à capacidade do Stream Analytics de processar um grande número de eventos em tempo real, a aplicação é altamente escalável sem a necessidade de aumentar a camada da loja de dados. A camada de loja de dados é essencialmente uma visão materializada no sistema. A saída do [Azure Stream Analytics para o Azure Cosmos DB](stream-analytics-documentdb-output.md) descreve como o Cosmos DB é usado como uma saída stream analytics.

Em aplicações reais onde a lógica de processamento é complexa e há a necessidade de atualizar determinadas partes da lógica de forma independente, vários trabalhos stream analytics podem ser compostos juntamente com Os Hubs de Eventos como o intermediário de eventos.

![App de sourcing de eventos complexos DAASA](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

Este padrão melhora a resiliência e a capacidade de gestão do sistema. No entanto, embora o Stream Analytics garanta exatamente uma vez que o processamento, há uma pequena possibilidade de duplicar eventos pode aterrar nos Centros de Eventos intermediários. É importante para o trabalho a jusante stream analytics para enganar eventos usando chaves lógicas em uma janela de retrospetiva. Para mais informações sobre a entrega do evento, consulte a referência da Entrega de [Eventos.](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)

## <a name="use-reference-data-for-application-customization"></a>Utilizar dados de referência para personalização de aplicações

A funcionalidade de dados de referência Azure Stream Analytics foi concebida especificamente para personalização de utilizadores finais, como limiar de alerta, regras de processamento e [geovedações.](geospatial-scenarios.md) A camada de aplicação pode aceitar alterações de parâmetros e armazená-las numa base de dados SQL. O trabalho do Stream Analytics consulta periodicamente as alterações da base de dados e torna os parâmetros de personalização acessíveis através de uma adesão de dados de referência. Para obter mais informações sobre como utilizar dados de referência para personalização de aplicações, consulte a [adesão de](/stream-analytics-query/reference-data-join-azure-stream-analytics)dados de [referência SQL](sql-reference-data.md) e dados de referência .

Este padrão também pode ser utilizado para implementar um motor de regras onde os limiares das regras são definidos a partir de dados de referência. Para obter mais informações sobre as regras, consulte [as regras configuradas do Processo no Azure Stream Analytics](stream-analytics-threshold-based-rules.md).

![App de dados de referência ASA](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Adicione machine learning às suas ideias em tempo real

O modelo integrado de [deteção](stream-analytics-machine-learning-anomaly-detection.md) de anomalias da Azure Stream Analytics é uma forma conveniente de introduzir machine learning na sua aplicação em tempo real. Para uma gama mais alargada de necessidades de Machine Learning, o [Azure Stream Analytics integra-se com o serviço de pontuação da Azure Machine Learning.](stream-analytics-machine-learning-integration-tutorial.md)

Para utilizadores avançados que queiram incorporar formação online e pontuar no mesmo pipeline Stream Analytics, veja este exemplo de como fazê-lo com [regressão linear.](stream-analytics-high-frequency-trading.md)

![App ASA Machine Learning](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>Perto de armazenamento de dados em tempo real

Outro padrão comum é o armazenamento de dados em tempo real, também chamado de armazém de dados de streaming. Além dos eventos que chegam ao Event Hubs e ao IoT Hub a partir da sua aplicação, o Azure Stream Analytics em [execução no IoT Edge](stream-analytics-edge.md) pode ser usado para satisfazer a limpeza de dados, a redução de dados e a loja de dados e as necessidades avançadas. O Stream Analytics que corre no IoT Edge pode lidar graciosamente com problemas de limitação de largura de banda e conectividade no sistema. O adaptador de saída SQL pode ser utilizado para a saída para o Armazém de Dados SQL; no entanto, a potência máxima está limitada a 10 MB/s.

![Armazenagem de Dados ASA](media/stream-analytics-solution-patterns/datawarehousing.png)

Uma maneira de melhorar a entrada com alguma sucatância de latência é arquivar os eventos no armazenamento da Blob Azure e, em seguida, [importá-los para o Armazém de Dados SQL com polybase](../synapse-analytics/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md). Deve coser manualmente a saída do Stream Analytics até ao armazenamento de blob e à entrada do armazenamento de blob para o SQL Data Warehouse, [arquivando os dados por carimbo](stream-analytics-custom-path-patterns-blob-storage-output.md) temporal e importando periodicamente.

Neste padrão de utilização, o Azure Stream Analytics é usado como um motor ETL quase em tempo real. Os eventos recém-chegados são continuamente transformados e armazenados para consumo de serviçode análise a jusante.

![Asa alta entrada Data Warehousing](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>Arquivar dados em tempo real para análise

A maioria das atividades de ciência de dados e analítica ainda acontecem offline. Os dados podem ser arquivados pelo Azure Stream Analytics através da saída da Azure Data Lake Store Gen2 e dos formatos de saída da Parquet. Esta capacidade remove o atrito para alimentar dados diretamente no Azure Data Lake Analytics, Azure Databricks e Azure HDInsight. O Azure Stream Analytics é utilizado como um motor ETL quase em tempo real nesta solução. Pode explorar dados arquivados no Data Lake utilizando vários motores de computação.

![Análise offline da ASA](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>Utilizar dados de referência para enriquecimento

O enriquecimento de dados é frequentemente um requisito para os motores ETL. O Azure Stream Analytics suporta o enriquecimento de dados com dados de [referência](stream-analytics-use-reference-data.md) tanto da base de dados SQL como do armazenamento do Azure Blob. O enriquecimento de dados pode ser feito para a aterragem de dados tanto no Lago de Dados Azure como no Armazém de Dados SQL.

![Análise offline da ASA com enriquecimento de dados](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>Operacionalizar insights a partir de dados arquivados

Se combinar o padrão de análise offline com o padrão de aplicação em tempo real, pode criar um ciclo de feedback. O ciclo de feedback permite que a aplicação se ajuste automaticamente para alterar padrões nos dados. Este ciclo de feedback pode ser tão simples como alterar o valor limiar para alertar, ou tão complexo como retreinar modelos de Machine Learning. A mesma solução a arquitetura pode ser aplicada tanto aos postos de trabalho da ASA que correm na nuvem como no IoT Edge.

![OPERACIONALIZAÇÃO de insights da ASA](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>Como monitorizar os empregos da ASA

Um trabalho de Azure Stream Analytics pode ser executado 24 horas por dia, 7 dias por semana, para processar os eventos de entrada continuamente em tempo real. A sua garantia de tempo de inatividade é crucial para a saúde da aplicação global. Enquanto o Stream Analytics é o único serviço de streaming de análise na indústria que oferece uma garantia de disponibilidade de [99,9%,](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/)ainda pode incorrer em algum nível de tempo de paragem. Ao longo dos anos, a Stream Analytics introduziu métricas, registos e estados de emprego para refletir a saúde dos empregos. Todas elas são surgidas através do serviço Azure Monitor e podem ser exportadas para OMS. Para obter mais informações, consulte [A monitorização](stream-analytics-monitoring.md)do trabalho do Stream Analytics e como monitorizar as consultas .

![Monitorização asa](media/stream-analytics-solution-patterns/monitoring.png)

Há duas coisas fundamentais a monitorizar:

- [Estado falhado do trabalho](job-states.md)

    Em primeiro lugar, tens de ter a certeza que o trabalho está a decorrer. Sem o trabalho no estado de execução, não são geradas novas métricas ou troncos. Os postos de trabalho podem mudar para um estado falhado por várias razões, incluindo ter um elevado nível de utilização da SU (isto é, ficar sem recursos).

- [Métricas de atraso de marca de água](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Esta métrica reflete a distância atrás do seu oleoduto de processamento no tempo do relógio da parede (segundos). Parte do atraso é atribuído à lógica de processamento inerente. Como resultado, monitorizar a tendência crescente é muito mais importante do que monitorizar o valor absoluto. O atraso constante do estado deve ser abordado pelo design da sua aplicação, não por monitorização ou alertas.

Após falhas, os [registos](stream-analytics-job-diagnostic-logs.md) de atividade e os registos de diagnóstico são os melhores locais para começar a procurar erros.

## <a name="build-resilient-and-mission-critical-applications"></a>Construir aplicações críticas resilientes e de missão

Independentemente da garantia SLA da Azure Stream Analytics e do cuidado com que executa a sua aplicação de ponta a ponta, as interrupções acontecem. Se a sua candidatura for crítica, tem de estar preparada para interrupções para recuperar graciosamente.

Para alertar as aplicações, o mais importante é detetar o próximo alerta. Pode optar por reiniciar o trabalho a partir da hora em que se recupera, ignorando alertas passados. A semântica do início do trabalho é na primeira hora de saída, não a primeira hora de entrada. A entrada é rebobinada para trás um período de tempo adequado para garantir que a primeira saída no tempo especificado está completa e correta. Não receberá agregados parciais e desencadeie alertas inesperadamente como resultado.

Também pode optar por iniciar a saída a partir de algum tempo no passado. Tanto os Hubs de Eventos como as políticas de retenção do IoT Hub possuem uma quantidade razoável de dados para permitir o processamento do passado. A troca é a rapidez com que pode alcançar o tempo atual e começar a gerar novos alertas atempados. Os dados perdem o seu valor rapidamente ao longo do tempo, por isso é importante recuperar rapidamente o tempo atual. Há duas maneiras de recuperar rapidamente:

- Fornecer mais recursos (SU) ao recuperar.
- Reinicie a partir do tempo atual.

Reiniciar a partir do momento atual é simples de fazer, com a compensação de deixar uma lacuna durante o processamento. Reiniciar desta forma pode ser bom para alertar cenários, mas pode ser problemático para cenários de dashboard e é um não-iniciante para cenários de arquivamento e armazenamento de dados.

O fornecimento de mais recursos pode acelerar o processo, mas o efeito de ter um aumento da taxa de processamento é complexo.

- Teste que o seu trabalho é escalável para um maior número de US. Nem todas as consultas são escaláveis. Tens de te certificar que a tua consulta é [paralelizada.](stream-analytics-parallelization.md)

- Certifique-se de que existem divisórias suficientes nos Centros de Eventos a montante ou no Hub IoT, que pode adicionar mais Unidades de Produção (TUs) para escalar a entrada de entrada. Lembre-se, cada Event Hubs TU atinge o máximo a uma taxa de saída de 2 MB/s.

- Certifique-se de que disponibilizou recursos suficientes nos asinks de saída (isto é, Base de Dados SQL, Cosmos DB), para que não acelerem o aumento da saída, o que por vezes pode fazer com que o sistema bloqueie.

O mais importante é antecipar a mudança da taxa de processamento, testar estes cenários antes de entrar em produção, e estar pronto para escalar o processamento corretamente durante o tempo de recuperação do insucesso.

No cenário extremo de que os eventos de entrada estão todos atrasados, é possível que [todos os eventos atrasados sejam retirados](stream-analytics-time-handling.md) se tiver aplicado uma janela de chegada tardia ao seu trabalho. A queda dos acontecimentos pode parecer um comportamento misterioso no início; no entanto, considerando que o Stream Analytics é um motor de processamento em tempo real, espera que os eventos de entrada estejam perto do tempo do relógio da parede. Tem de desistir de eventos que violem estes constrangimentos.

### <a name="lambda-architectures-or-backfill-process"></a>Lambda Architectures ou processo de backfill

Felizmente, o padrão de arquivamento de dados anterior pode ser usado para processar estes eventos tardios graciosamente. A ideia é que o arquivamento de trabalho processa eventos de entrada na hora de chegada e arquiva eventos no balde de tempo certo em Azure Blob ou Azure Data Lake Store com a sua hora de evento. Não importa a chegada de um evento, nunca será abandonado. Sempre pousará no balde do tempo certo. Durante a recuperação, é possível reprocessar os eventos arquivados e repor os resultados na loja de eleição. Isto é semelhante ao modo como os padrões de lambda são implementados.

![Recarga de backfill ASA](media/stream-analytics-solution-patterns/backfill.png)

O processo de backfill tem de ser feito com um sistema de processamento de lotes offline, que provavelmente tem um modelo de programação diferente do Azure Stream Analytics. Isto significa que tens de reimplementar toda a lógica de processamento.

Para o recheio, ainda é importante, pelo menos temporariamente, fornecer mais recursos aos lavatórios de saída para lidar com uma produção mais elevada do que as necessidades constantes de processamento do estado.

|Cenários  |Recomeçar a partir de agora apenas  |Reinício da última paragem |Reinicie a partir de agora + recarga com eventos arquivados|
|---------|---------|---------|---------|
|**Dashboarding**   |Cria lacuna    |OK para short outage    |Utilização para interrupção prolongada |
|**Alertas**   |Aceitável |OK para short outage    |Não é necessário |
|**App de sourcing de eventos** |Aceitável |OK para short outage    |Utilização para interrupção prolongada |
|**Armazenamento de dados**   |Perda de dados  |Aceitável |Não é necessário |
|**Análise offline**  |Perda de dados  |Aceitável |Não é necessário|

## <a name="putting-it-all-together"></a>Juntar tudo

Não é difícil imaginar que todos os padrões de solução acima mencionados possam ser combinados num complexo sistema de ponta a ponta. O sistema combinado pode incluir dashboards, alerta, aplicação de fornecimento de eventos, armazenamento de dados e capacidades de análise offline.

A chave é projetar o seu sistema em padrões composáveis, para que cada subsistema possa ser construído, testado, atualizado e recuperado de forma independente.

## <a name="next-steps"></a>Passos seguintes

Já viu uma variedade de padrões de solução usando o Azure Stream Analytics. Em seguida, pode criar o seu primeiro trabalho do Stream Analytics e experimentá-lo na prática:

* [Create a Stream Analytics job by using the Azure portal](stream-analytics-quick-create-portal.md) (Criar um trabalho do Stream Analytics com o portal do Azure).
* [Crie um trabalho de Stream Analytics utilizando o Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Crie um trabalho de Stream Analytics utilizando o Visual Studio](stream-analytics-quick-create-vs.md).
