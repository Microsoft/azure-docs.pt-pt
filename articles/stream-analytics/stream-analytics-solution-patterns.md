---
title: Padrões da solução Azure Stream Analytics
description: Conheça os padrões de solução comuns para o Azure Stream Analytics, tais como dashboarding, mensagens de eventos, lojas de dados, enriquecimento de dados de referência e monitorização.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 1bd3c1099344bd266d7e3bc153613daaecfb412a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020320"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Padrões da solução Azure Stream Analytics

Como muitos outros serviços em Azure, o Stream Analytics é mais utilizado com outros serviços para criar uma solução de ponta a ponta maior. Este artigo discute soluções simples Azure Stream Analytics e vários padrões arquitetónicos. Pode basear-se nestes padrões para desenvolver soluções mais complexas. Os padrões descritos neste artigo podem ser usados numa grande variedade de cenários. Exemplos de padrões específicos do cenário estão disponíveis nas arquiteturas de [soluções Azure.](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics)

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>Crie um trabalho stream Analytics para potenciar a experiência de dashboarding em tempo real

Com o Azure Stream Analytics, pode levantar-se rapidamente em tempo real de painéis e alertas. Uma solução simples ingere eventos de Event Hubs ou IoT Hub, e [alimenta o painel power BI com um conjunto de dados de streaming](/power-bi/service-real-time-streaming). Para obter mais informações, consulte o tutorial detalhado [Analisar dados de chamadas fraudulentas com Stream Analytics e visualizar os resultados no painel Power BI](stream-analytics-real-time-fraud-detection.md).

![Painel DE BI DE ENERGIA ASA](media/stream-analytics-solution-patterns/power-bi-dashboard.png)

Esta solução pode ser construída a poucos minutos do portal Azure. Não há nenhuma codificação extensiva envolvida, e a linguagem SQL é usada para expressar a lógica do negócio.

Este padrão de solução oferece a latência mais baixa desde a fonte do evento até ao painel Power BI num browser. O Azure Stream Analytics é o único serviço Azure com esta capacidade incorporada.

## <a name="use-sql-for-dashboard"></a>Use SQL para painel de instrumentos

O painel Power BI oferece baixa latência, mas não pode ser usado para produzir relatórios power bi completos. Um padrão comum de reporte é fazer a produção dos seus dados para a Base de Dados SQL primeiro. Em seguida, utilize o conector SQL da Power BI para consultar o SQL para obter os dados mais recentes.

![Painel ASA SQL](media/stream-analytics-solution-patterns/sql-dashboard.png)

A utilização da SQL Database dá-lhe mais flexibilidade, mas à custa de uma latência ligeiramente maior. Esta solução é ideal para empregos com requisitos de latência superiores a um segundo. Com este método, você pode maximizar as capacidades do Power BI para cortar e dados mais para relatórios, e muito mais opções de visualização. Também ganha a flexibilidade de usar outras soluções de dashboard, como o Tableau.

O SQL não é uma loja de dados de alto rendimento. A produção máxima para a Base de Dados SQL da Azure Stream Analytics é atualmente de cerca de 24 MB/s. Se as fontes de evento na sua solução produzirem dados a um ritmo mais elevado, é necessário utilizar a lógica de processamento no Stream Analytics para reduzir a taxa de saída para o SQL. Técnicas como filtragem, agregados à janela, padrão correspondente a juntas temporais e funções analíticas podem ser usadas. A taxa de saída para SQL pode ser otimizada através de técnicas descritas na [saída Azure Stream Analytics para Azure SQL Database](stream-analytics-sql-output-perf.md).

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Incorporar insights em tempo real na sua aplicação com mensagens de evento

O segundo uso mais popular do Stream Analytics é gerar alertas em tempo real. Neste padrão de solução, a lógica de negócio no Stream Analytics pode ser usada para detetar [padrões temporais e espaciais](stream-analytics-geospatial-functions.md) ou [anomalias,](stream-analytics-machine-learning-anomaly-detection.md)em seguida, produzir sinais de alerta. No entanto, ao contrário da solução do dashboard onde o Stream Analytics utiliza o Power BI como ponto final preferido, uma série de sumidouros de dados intermédios podem ser utilizados. Estes lavatórios incluem Centros de Eventos, Service Bus e Azure Functions. Você, como construtor de aplicações, precisa decidir quais os dados que o lavatório funciona melhor para o seu cenário.

A jusante, a lógica do consumidor de eventos deve ser implementada para gerar alertas no seu fluxo de trabalho de negócio existente. Como pode implementar lógica personalizada em Funções Azure, as Funções Azure são a forma mais rápida de realizar esta integração. Um tutorial para a utilização da Função Azure como saída para um trabalho de Stream Analytics pode ser encontrado em [Funções Run Azure a partir de trabalhos Azure Stream Analytics](stream-analytics-with-azure-functions.md). A Azure Functions também suporta vários tipos de notificações, incluindo texto e e-mail. A Logic App também pode ser usada para tal integração, com Os Centros de Eventos entre Stream Analytics e Logic App.

![App de mensagens de evento ASA](media/stream-analytics-solution-patterns/event-messaging-app.png)

O Event Hubs, por outro lado, oferece o ponto de integração mais flexível. Muitos outros serviços, como o Azure Data Explorer e o Time Series Insights podem consumir eventos a partir de Centros de Eventos. Os serviços podem ser conectados diretamente ao evento Hubs afundar a partir do Azure Stream Analytics para completar a solução. O Event Hubs é também o corretor de mensagens de maior produção disponível no Azure para tais cenários de integração.

## <a name="dynamic-applications-and-websites"></a>Aplicações dinâmicas e websites

Pode criar visualizações personalizadas em tempo real, tais como visualização de dashboard ou mapa, utilizando o Azure Stream Analytics e o Serviço Azure SignalR. Utilizando o SignalR, os clientes web podem ser atualizados e mostrar conteúdo dinâmico em tempo real.

![App dinâmica ASA](media/stream-analytics-solution-patterns/dynamic-app.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Incorporar insights em tempo real na sua aplicação através de lojas de dados

A maioria dos serviços web e aplicações web hoje usam um padrão de pedido/resposta para servir a camada de apresentação. O padrão de pedido/resposta é simples de construir e pode ser facilmente dimensionado com tempo de resposta baixo usando um frontend apátrida e lojas escaláveis, como cosmos DB.

O elevado volume de dados cria frequentemente estrangulamentos de desempenho num sistema baseado em CRUD. O [padrão de solução de fornecimento de eventos](/azure/architecture/patterns/event-sourcing) é usado para resolver os estrangulamentos de desempenho. Padrões temporais e insights também são difíceis e ineficientes de extrair de uma loja de dados tradicional. Aplicações modernas orientadas por dados de alto volume muitas vezes adotam uma arquitetura baseada em fluxo de dados. Azure Stream Analytics como o motor de computação para dados em movimento é um eixo nessa arquitetura.

![App de sourcing de eventos ASA](media/stream-analytics-solution-patterns/event-sourcing-app.png)

Neste padrão de solução, os eventos são processados e agregados em data stores pela Azure Stream Analytics. A camada de aplicação interage com as lojas de dados utilizando o padrão tradicional de pedido/resposta. Devido à capacidade do Stream Analytics de processar um grande número de eventos em tempo real, a aplicação é altamente escalável sem a necessidade de aumentar a camada da loja de dados. A camada de armazenamento de dados é essencialmente uma visão materializada no sistema. [A saída do Azure Stream Analytics para Azure Cosmos DB](stream-analytics-documentdb-output.md) descreve como cosmos DB é usado como uma saída stream Analytics.

Em aplicações reais onde a lógica de processamento é complexa e há a necessidade de atualizar certas partes da lógica de forma independente, vários trabalhos stream Analytics podem ser compostos juntamente com o Event Hubs como corretor de eventos intermediário.

![App de sourcing de eventos complexo asa](media/stream-analytics-solution-patterns/event-sourcing-app-complex.png)

Este padrão melhora a resiliência e a gestão do sistema. No entanto, embora o Stream Analytics garanta exatamente uma vez que o processamento, há uma pequena chance de que eventos duplicados possam aterrar nos Centros de Eventos intermediários. É importante para o trabalho a jusante stream Analytics dedupe eventos usando chaves lógicas em uma janela de olhar. Para obter mais informações sobre a entrega do evento, consulte a referência [Garantias de Entrega de Eventos.](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)

## <a name="use-reference-data-for-application-customization"></a>Utilize dados de referência para personalização de aplicações

A funcionalidade de dados de referência Azure Stream Analytics foi concebida especificamente para personalização do utilizador final, como o limiar de alerta, regras de processamento e [geofences.](geospatial-scenarios.md) A camada de aplicação pode aceitar alterações de parâmetros e armazená-las na Base de Dados SQL. O trabalho stream Analytics periodicamente questiona as alterações a partir da base de dados e torna os parâmetros de personalização acessíveis através de uma união de dados de referência. Para obter mais informações sobre como utilizar dados de referência para personalização de aplicações, consulte [os dados de referência do SQL](sql-reference-data.md) e [os dados de referência juntam-se.](/stream-analytics-query/reference-data-join-azure-stream-analytics)

Este padrão também pode ser usado para implementar um motor de regras onde os limiares das regras são definidos a partir de dados de referência. Para obter mais informações sobre regras, consulte [regras baseadas em limiares de processos em Azure Stream Analytics](stream-analytics-threshold-based-rules.md).

![App de dados de referência ASA](media/stream-analytics-solution-patterns/reference-data-app.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Adicione machine learning aos seus insights em tempo real

O modelo de deteção de [anomalias](stream-analytics-machine-learning-anomaly-detection.md) incorporada do Azure Stream Analytics é uma forma conveniente de introduzir machine learning na sua aplicação em tempo real. Para uma ampla gama de necessidades de Machine Learning, consulte [o Azure Stream Analytics integra-se com o serviço de pontuação da Azure Machine Learning.](stream-analytics-machine-learning-integration-tutorial.md)

Para utilizadores avançados que pretendam incorporar formação on-line e pontuação no mesmo pipeline Stream Analytics, veja este exemplo de como fazê-lo com [regressão linear.](stream-analytics-high-frequency-trading.md)

![App ASA Machine Learning](media/stream-analytics-solution-patterns/machine-learning-app.png)

## <a name="real-time-data-warehousing"></a>Armazenagem de dados em tempo real

Outro padrão comum é o armazenamento de dados em tempo real, também chamado de armazém de dados de streaming. Além dos eventos que chegam aos Event Hubs e IoT Hub a partir da sua aplicação, [o Azure Stream Analytics em execução no IoT Edge](stream-analytics-edge.md) pode ser usado para satisfazer a limpeza de dados, redução de dados e armazenamento de dados e necessidades avançadas. Stream Analytics em execução no IoT Edge pode lidar graciosamente com problemas de limitação de largura de banda e conectividade no sistema. Stream Analytics pode suportar taxas de produção de até 200MB/seg enquanto escreve para Azure Synapse Analytics.

![Armazenagem de dados ASA](media/stream-analytics-solution-patterns/data-warehousing.png)


## <a name="archiving-real-time-data-for-analytics"></a>Arquivar dados em tempo real para análise

A maioria das atividades de ciência de dados e análises ainda acontecem offline. Os dados podem ser arquivados pelo Azure Stream Analytics através da saída gen2 da Azure Data Lake Store e dos formatos de saída do Parquet. Esta capacidade remove o atrito para alimentar os dados diretamente no Azure Data Lake Analytics, Azure Databricks e Azure HDInsight. O Azure Stream Analytics é utilizado como um motor ETL em tempo real nesta solução. Você pode explorar dados arquivados em Data Lake usando vários motores de computação.

> [!div class="mx-imgBorder"]
> ![Análise offline asA](media/stream-analytics-solution-patterns/offline-analytics.png)

## <a name="use-reference-data-for-enrichment"></a>Utilizar dados de referência para enriquecimento

O enriquecimento de dados é frequentemente um requisito para os motores ETL. O Azure Stream Analytics suporta o enriquecimento de dados com [dados](stream-analytics-use-reference-data.md) de referência tanto da Base de Dados SQL como do armazenamento de Azure Blob. O enriquecimento de dados pode ser feito para a aterragem de dados tanto no Azure Data Lake como no Azure Synapse Analytics.


![Análise offline da ASA com enriquecimento de dados](media/stream-analytics-solution-patterns/offline-analytics-enriched.png)

## <a name="operationalize-insights-from-archived-data"></a>Operacionalizar insights a partir de dados arquivados

Se combinar o padrão de análise offline com o padrão de aplicação em tempo real, pode criar um ciclo de feedback. O ciclo de feedback permite que a aplicação se ajuste automaticamente para alterar padrões nos dados. Este ciclo de feedback pode ser tão simples como alterar o valor limiar para alertar, ou tão complexo como retraining machine learning modelos. A mesma solução de arquitetura pode ser aplicada tanto para trabalhos ASA que correm na nuvem como em IoT Edge.

![AsA insights operacionalização](media/stream-analytics-solution-patterns/insights-operationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>Como monitorizar os empregos da ASA

Um trabalho Azure Stream Analytics pode ser executado 24 horas por dia para processar eventos de entrada continuamente em tempo real. A sua garantia de uptime é crucial para a saúde da aplicação global. Embora o Stream Analytics seja o único serviço de streaming de análise na indústria que oferece uma  [garantia de disponibilidade de 99,9%,](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/)ainda pode incorrer em algum nível de tempo de mente. Ao longo dos anos, a Stream Analytics introduziu métricas, registos e estados de trabalho para refletir a saúde dos trabalhos. Todos eles são emergidos através do serviço Azure Monitor e podem ser exportados para o OMS. Para obter mais informações, consulte a monitorização do trabalho do [Stream Analytics e como monitorizar as consultas.](stream-analytics-monitoring.md)

![Monitorização asa](media/stream-analytics-solution-patterns/monitoring.png)

Há duas coisas fundamentais para monitorizar:

- [Trabalho falhou estado](job-states.md)

    Em primeiro lugar, tens de ter a certeza que o trabalho está a funcionar. Sem o trabalho no estado de funcionamento, não são geradas novas métricas ou registos. Os postos de trabalho podem mudar para um estado falhado por várias razões, incluindo ter um elevado nível de utilização de SU (isto é, ficar sem recursos).

- [Métricas de atraso da marca de água](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Esta métrica reflete a distância atrás do seu gasoduto de processamento na hora do relógio de parede (segundos). Parte do atraso é atribuído à lógica de processamento inerente. Como resultado, o acompanhamento da tendência crescente é muito mais importante do que monitorizar o valor absoluto. O atraso constante do estado deve ser abordado pelo desenho da sua aplicação, não através da monitorização ou alertas.

Após a falha, registos de atividades e [registos de diagnóstico](stream-analytics-job-diagnostic-logs.md) são os melhores locais para começar a procurar erros.

## <a name="build-resilient-and-mission-critical-applications"></a>Construir aplicações críticas resilientes e de missão

Independentemente da garantia SLA do Azure Stream Analytics e do cuidado com que executou a aplicação de ponta a ponta, as interrupções acontecem. Se a sua aplicação é uma missão crítica, tem de estar preparada para as interrupções para se recuperar graciosamente.

Para alertar as aplicações, o mais importante é detetar o próximo alerta. Pode optar por reiniciar o trabalho a partir do momento em que se recupera, ignorando alertas passados. A semântica de início de trabalho é pela primeira vez, não pela primeira vez. A entrada é rebobinada para trás um tempo adequado para garantir que a primeira saída no tempo especificado está completa e correta. Não receberá agregados parciais e alertas de disparo inesperadamente como resultado.

Também pode optar por iniciar a saída a partir de algum tempo no passado. Tanto os Centros de Eventos como as políticas de retenção do IoT Hub possuem uma quantidade razoável de dados para permitir o processamento do passado. A troca é a rapidez com que se consegue acompanhar o tempo atual e começar a gerar novos alertas em tempo oportuno. Os dados perdem o seu valor rapidamente ao longo do tempo, por isso é importante acompanhar rapidamente o tempo atual. Há duas maneiras de recuperar rapidamente:

- Disponibilização de mais recursos (SU) ao recuperar.
- Reiniciar a partir da hora atual.

Reiniciar da corrente o tempo é simples de fazer, com a troca de deixar uma lacuna durante o processamento. Reiniciar desta forma pode ser bom para alertar cenários, mas pode ser problemático para cenários de dashboard e é um não-arranque para cenários de arquivamento e armazenamento de dados.

O fornecimento de mais recursos pode acelerar o processo, mas o efeito de ter um aumento da taxa de processamento é complexo.

- Teste que o seu trabalho é escalável para um maior número de SUs. Nem todas as consultas são escaláveis. Tem de se certificar de que a sua consulta está [paralelamente](stream-analytics-parallelization.md).

- Certifique-se de que existem divisórias suficientes nos Centros de Eventos a montante ou no Hub IoT que pode adicionar mais Unidades de Produção (TUs) para escalar a entrada. Lembre-se, cada Event Hubs TU atinge um máximo de produção de 2 MB/s.

- Certifique-se de que abasteca recursos suficientes nos lavatórios de saída (isto é, SQL Database, Cosmos DB), para que não adquiram o aumento da produção, o que por vezes pode fazer com que o sistema bloqueie.

O mais importante é antecipar a alteração da taxa de processamento, testar estes cenários antes de entrar em produção e estar pronto para escalar o processamento corretamente durante o tempo de recuperação da falha.

No cenário extremo de que os eventos de entrada estão todos [atrasados, é possível que todos os eventos atrasados sejam retirados](stream-analytics-time-handling.md) se tiver aplicado uma janela de chegada tardia ao seu trabalho. A queda dos acontecimentos pode parecer um comportamento misterioso no início; no entanto, tendo em conta que o Stream Analytics é um motor de processamento em tempo real, espera que os eventos de entrada estejam perto da hora do relógio de parede. Tem de largar eventos que violem estes constrangimentos.

### <a name="lambda-architectures-or-backfill-process"></a>Lambda Arquiteturas ou Processo de Enchimento

Felizmente, o padrão de arquivamento de dados anterior pode ser usado para processar estes eventos tardios graciosamente. A ideia é que o trabalho de arquivamento processa eventos de chegada na hora de chegada e arquiva eventos no balde da hora certa em Azure Blob ou Azure Data Lake Store com a sua hora de evento. Não importa quão tarde um evento chegue, nunca será largado. Sempre pousará no balde do tempo certo. Durante a recuperação, é possível reprocessar os eventos arquivados e preencher os resultados para a loja de eleição. Isto é semelhante à forma como os padrões lambda são implementados.

![Enchimento de asa](media/stream-analytics-solution-patterns/back-fill.png)

O processo de enchimento tem de ser feito com um sistema de processamento de lotes offline, que provavelmente tem um modelo de programação diferente do Azure Stream Analytics. Isto significa que tem de voltar a implementar toda a lógica de processamento.

Para o enchimento, ainda é importante pelo menos provisoriamente mais recursos para os lavatórios de saída para lidar com uma produção mais elevada do que as necessidades de processamento do estado constante.

|Cenários  |Reinicie a partir de agora apenas  |Recomeçar da última vez parada |Reiniciar a partir de agora + enchimento com eventos arquivados|
|---------|---------|---------|---------|
|**Dashboarding**   |Cria lacunas    |OK para paragem curta    |Utilização para paragem prolongada |
|**Alertas**   |Aceitável |OK para paragem curta    |Não é necessário |
|**App de sourcing de eventos** |Aceitável |OK para paragem curta    |Utilização para paragem prolongada |
|**Armazenagem de dados**   |Perda de dados  |Aceitável |Não é necessário |
|**Análise offline**  |Perda de dados  |Aceitável |Não é necessário|

## <a name="putting-it-all-together"></a>Juntar tudo

Não é difícil imaginar que todos os padrões de solução acima mencionados possam ser combinados num complexo sistema de ponta a ponta. O sistema combinado pode incluir dashboards, alerta, aplicação de fornecimento de eventos, armazenamento de dados e capacidades de análise offline.

A chave é projetar o seu sistema em padrões compósíveis, para que cada subsistema possa ser construído, testado, atualizado e recuperado de forma independente.

## <a name="next-steps"></a>Passos seguintes

Já viu uma variedade de padrões de solução usando a Azure Stream Analytics. Em seguida, pode criar o seu primeiro trabalho do Stream Analytics e experimentá-lo na prática:

* [Create a Stream Analytics job by using the Azure portal](stream-analytics-quick-create-portal.md) (Criar um trabalho do Stream Analytics com o portal do Azure).
* [Crie um trabalho stream analytics utilizando a Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Crie um trabalho stream analytics utilizando o Visual Studio](stream-analytics-quick-create-vs.md).
