---
title: Padrões de solução do Stream Analytics do Azure
description: Saiba mais sobre os padrões de solução para o Azure Stream Analytics.
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 4f4f22628d2c2a6beb7974aa9b776a2148a3fee0
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65238066"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Padrões de solução do Stream Analytics do Azure

Como muitos outros serviços do Azure, o Stream Analytics melhor é utilizado para criar uma solução ponto-a-ponto maior com outros serviços. Este artigo aborda as soluções do Azure Stream Analytics simples e vários padrões de arquiteturais. Pode se basear estes padrões para desenvolver soluções mais complexas. Os padrões descritos neste artigo podem ser usados numa ampla variedade de cenários. Exemplos de padrões específicos de cenário estão disponíveis na [arquiteturas de soluções do Azure](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics).

## <a name="create-a-stream-analytics-job-with-a-real-time-dashboard"></a>Criar uma tarefa do Stream Analytics com um dashboard em tempo real

Com a facilidade de Azure Stream Analytics de utilização, pode definir rapidamente dashboards em tempo real e alertas. Uma solução simples ingere eventos a partir dos Hubs de eventos ou o IoT Hub, e [feeds o dashboard do Power BI com um conjunto de dados de transmissão em fluxo](/power-bi/service-real-time-streaming). Para obter mais informações, veja o tutorial detalhado [analisar dados de chamada telefónica com o Stream Analytics e visualizar os resultados num dashboard do Power BI](stream-analytics-manage-job.md).

![Dashboard do ASA Power BI](media/stream-analytics-solution-patterns/pbidashboard.png)

Essa solução pode ser criada em apenas alguns minutos a partir do portal do Azure. Não existe que nenhuma codificação extensa envolvido e linguagem SQL é usada para expressar a lógica de negócios.

Este padrão de solução de dashboard em tempo real oferece a latência mais baixa da origem de evento para o dashboard do Power BI num browser. O Azure Stream Analytics é o serviço apenas do Azure com esta capacidade incorporada.

## <a name="use-sql-for-dashboard"></a>Utilizar o SQL para o dashboard

O dashboard do Power BI oferece baixa latência, mas não pode ser utilizada para produzir relatórios de Power BI apta completos. Um padrão comum de geração de relatórios é primeiro os seus dados para uma base de dados do SQL de saída. Em seguida, utilize o conector do SQL do Power BI para a consulta SQL para os dados mais recentes.

![Dashboard do ASA SQL](media/stream-analytics-solution-patterns/sqldashboard.png)

Com o SQL base de dados dá-lhe mais flexibilidade a custa de maior latência. Esta solução é ideal para as tarefas com requisitos de latência maiores do que um segundo. Com esse método, pode maximizar o utilitário do Power BI para o setor adicional e decomposição os dados para relatórios. Também ganha a flexibilidade de usar outras soluções de dashboard, como o Tableau.

SQL não é um arquivo de dados de alto débito e o débito máximo para uma base de dados SQL do Azure Stream Analytics é 24 MB/s. Se a origens de eventos na sua solução de produzem os dados a uma tarifa mais alta, terá de usar a lógica de processamento no Stream Analytics para reduzir a taxa de saída para o SQL. Técnicas, como a filtragem, agregados em janelas, o padrão de correspondência com associações temporais e funções de análise podem ser utilizadas. A taxa de saída para o SQL pode ser mais otimizada com as técnicas descritas [saída de Azure Stream Analytics para a base de dados do Azure SQL](stream-analytics-sql-output-perf.md).

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Incorporar informações em tempo real na sua aplicação com as mensagens de evento

A utilização em segundo lugar mais popular do Stream Analytics é gerar alertas em tempo real. Neste padrão de solução, a lógica de negócios no Stream Analytics pode ser utilizada para detetar [padrões temporais e geográficos](stream-analytics-geospatial-functions.md) ou [anomalias](stream-analytics-machine-learning-anomaly-detection.md), em seguida, produzir sinais de alerta. No entanto, ao contrário da solução de dashboard onde o Stream Analytics utiliza o Power BI como um ponto de extremidade preferido, pode ser utilizado um número de Coletores de dados intermediários. Estes sinks incluem os Hubs de eventos do Service Bus e as funções do Azure. Como o construtor de aplicação, terá de decidir qual sink de dados funciona melhor para seu cenário.

Lógica de consumidor de eventos de Downstream deve ser implementada para gerar alertas no seu fluxo de trabalho de negócios existentes. Uma vez que pode implementar a lógica personalizada nas funções do Azure, as funções são a forma mais rápida, pode efetuar esta integração. Um tutorial para utilizar a função do Azure como a saída de uma tarefa do Stream Analytics pode ser encontrada na [executar as funções do Azure das tarefas do Azure Stream Analytics](stream-analytics-with-azure-functions.md). As funções do Azure também oferece suporte a vários tipos de notificações, incluindo texto e e-mail. Aplicação lógica também pode ser utilizada para essa integração, com os Hubs de eventos entre o Stream Analytics e a aplicação lógica.

![Aplicação de mensagens de evento do ASA](media/stream-analytics-solution-patterns/eventmessagingapp.png)

Os Hubs de eventos, por outro lado, oferece o ponto de integração mais flexível. Muitos outros serviços, como o Explorador de dados do Azure e o Time Series Insight, podem consumir eventos dos Hubs de eventos. Os serviços podem ser ligados diretamente para o sink de Hubs de eventos do Azure Stream Analytics para concluir a solução. Os Hubs de eventos também é o mais alto débito Mediador de mensagens disponível no Azure para estes cenários de integração.

## <a name="dynamic-applications-and-websites"></a>Web sites e aplicativos dinâmicos

Pode criar visualizações personalizadas de em tempo real, como o dashboard ou mapear a visualização, com o Azure Stream Analytics e o serviço Azure SignalR. Com o SignalR, os clientes web podem ser atualizados e mostram conteúdo dinâmico em tempo real.

![Aplicação dinâmica do ASA](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Incorporar informações em tempo real na sua aplicação por meio de arquivos de dados

A maioria dos aplicativos web e serviços da web hoje mesmo usar um padrão de solicitação/resposta para servir a camada de apresentação. O padrão de solicitação/resposta é simples de criar e podem ser facilmente dimensionados com tempo de resposta de baixa com um front-end sem estado e armazenamentos dimensionáveis, como o Cosmos DB.

Volume de dados de alta cria, muitas vezes, afunilamentos de desempenho num sistema baseado em CRUD. O [padrão de solução de origem do evento](/azure/architecture/patterns/event-sourcing.md) é utilizado para resolver os afunilamentos de desempenho. Padrões temporais e informações também são difíceis e ineficiente para extrair um arquivo de dados tradicionais. Dados de grande volume modernos controlado por aplicativos, muitas vezes, adotam uma arquitetura baseada em fluxo de dados. O Azure Stream Analytics, como o motor de computação para os dados em movimento é uma maior nessa arquitetura.

![Aplicação de fornecimento de eventos do ASA](media/stream-analytics-solution-patterns/eventsourcingapp.png)

Neste padrão de solução, os eventos são processados e agregados em arquivos de dados pelo Azure Stream Analytics. A camada de aplicativo interage com os arquivos de dados usando o padrão de solicitação/resposta tradicional. Devido à capacidade do Stream Analytics para processar um grande número de eventos em tempo real, a aplicação é altamente escalável sem a necessidade de aumentar a camada de arquivo de dados. A camada de arquivo de dados é, essencialmente, uma vista materializada no sistema. [Saída do Azure Stream Analytics ao Azure Cosmos DB](stream-analytics-documentdb-output.md) descreve como o Cosmos DB é utilizado como uma saída Stream Analytics.

Em aplicativos reais, lógica de processamento onde é complexo e aqui é a necessidade de atualizar determinadas partes da lógica de forma independente, podem ser composto por várias tarefas do Stream Analytics, juntamente com os Hubs de eventos como o Mediador de eventos de intermediário.

![Aplicação de fornecimento de eventos complexos do ASA](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

Este padrão melhora a resiliência e a capacidade de gerenciamento do sistema. No entanto, mesmo que o Stream Analytics garante o processamento exatamente uma vez, há uma chance de pequenas que eventos duplicados podem apresentar o Hubs de eventos intermediário. É importante para a tarefa de Stream Analytics downstream para eventos de utilização de chaves de lógica numa janela lookback para eliminação de duplicados. Para obter mais informações sobre a entrega de eventos, consulte [garantias de entrega de eventos](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) referência.

## <a name="use-reference-data-for-application-customization"></a>Utilizar dados de referência para a personalização de aplicativos

A funcionalidade de dados de referência do Azure Stream Analytics foi concebida especificamente para personalização do utilizador final, como alertas de limiar, regras de processamento, e [perímetros geográficos](geospatial-scenarios.md). A camada de aplicação pode aceitar as alterações do parâmetro e armazená-los numa base de dados SQL. A tarefa do Stream Analytics periodicamente consultas para alterações da base de dados e faz com que os parâmetros de personalização acessível por meio de uma associação de dados de referência. Para obter mais informações sobre como utilizar os dados de referência para a personalização de aplicativos, consulte [dados de referência do SQL](sql-reference-data.md) e [associação de dados de referência](/stream-analytics-query/reference-data-join-azure-stream-analytics).

Este padrão também pode ser usado para implementar um mecanismo de regras em que os limiares das regras são definidos de dados de referência. Para obter mais informações sobre regras, consulte [processar regras configuráveis baseados no limiar no Azure Stream Analytics](stream-analytics-threshold-based-rules.md).

![Aplicação de dados de referência do ASA](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Adicionar o Machine Learning para suas informações em tempo real

Incorporado do Stream Analytics do Azure [modelo de deteção de anomalias](stream-analytics-machine-learning-anomaly-detection.md) é uma forma conveniente para apresentar o Machine Learning à sua aplicação em tempo real. Para necessidades de uma ampla gama de Machine Learning, consulte [do Azure Stream Analytics integra-se com o serviço de classificação do Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

Para os utilizadores avançados que desejam incorporar a formação online e de classificação no mesmo pipeline do Stream Analytics, veja este exemplo de como fazer isso com [regressão linear](stream-analytics-high-frequency-trading.md).

![Aplicação de Machine Learning do ASA](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>Junto ao armazenamento de dados em tempo real

Outro padrão comum é dados em tempo real de armazenamento, também denominado de armazém de dados de transmissão em fluxo. Além dos eventos que chegam ao IoT Hub e dos Hubs de eventos de seu aplicativo [em execução no IoT Edge do Azure Stream Analytics](stream-analytics-edge.md) pode ser usado para atender a limpeza dos dados, a redução de dados e arquivo de dados e necessidades de encaminhamento. Análise de Stream em execução no IoT Edge pode processar graciosamente limitação de largura de banda e problemas de conectividade no sistema. O adaptador de saída do SQL pode ser utilizado para a saída para o SQL Data Warehouse; No entanto, o débito máximo está limitado a 10 MB/s.

![ASA Data Warehousing](media/stream-analytics-solution-patterns/datawarehousing.png)

Uma forma de melhorar a taxa de transferência com alguns compensação de latência é arquivar os eventos para o armazenamento de Blobs do Azure e, em seguida [importá-los para o SQL Data Warehouse com o Polybase](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md). Tem manualmente reunir a saída do Stream Analytics para o armazenamento de BLOBs e a entrada do armazenamento de BLOBs para o SQL Data Warehouse por [arquivar os dados por timestamp](stream-analytics-custom-path-patterns-blob-storage-output.md) e importar periodicamente.

Neste padrão de utilização, o Azure Stream Analytics é utilizado como um mecanismo de ETL quase em tempo real. Recentemente evento que aconteça continuamente é transformadas e armazenadas para consumo do serviço de análise de downstream.

![Débito elevado ASA armazenamento de dados](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>Arquivamento de dados em tempo real para análise

A maioria das atividades de análise e ciência de dados ainda é ocorrer offline. Dados podem ser arquivados pelo Azure Stream Analytics através de saída de geração 2 do Azure Data Lake Store e no Parquet formatos de saída. Esse recurso remove o atrito para alimentar dados diretamente no Azure Data Lake Analytics, o Azure Databricks e o Azure HDInsight. O Azure Stream Analytics é utilizado como um mecanismo quase em tempo real de ETL nesta solução. Pode explorar os dados arquivados no Data Lake usando vários mecanismos de computação.

![Análise offline do ASA](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>Utilizar dados de referência para a melhoria do

Melhoramento de dados, muitas vezes, é um requisito para os mecanismos de ETL. O Azure Stream Analytics suporta enriquecimento de dados com [dados de referência](stream-analytics-use-reference-data.md) da base de dados SQL e armazenamento de Blobs do Azure. Melhoramento de dados pode ser feito para dados de destino no Azure Data Lake e no SQL Data Warehouse.

![Análise offline do ASA com enriquecimento de dados](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>Operacionalizar informações a partir dos dados arquivados

Se combinar o padrão de análise offline com o aplicativo em tempo real quase padrão, pode criar um ciclo de comentários. O ciclo de comentários permite que o aplicativo ajustar automaticamente para alterar os padrões nos dados. Este ciclo de feedback pode ser tão simples como alterar o valor de limiar de alerta, ou tão complexo como reparametrizar modelos do Machine Learning. A mesma arquitetura de solução pode ser aplicada a ambas as tarefas ASA em execução na cloud e no IoT Edge.

![Operacionalização de informações do ASA](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>Como monitorizar as tarefas ASA

Uma tarefa do Azure Stream Analytics pode ser executada 24x7 para processar eventos recebidos continuamente em tempo real. A garantia de tempo de atividade é crucial para o estado de funcionamento geral do aplicativo. Embora o Stream Analytics é o único serviço de análise de transmissão em fluxo da indústria que oferece um [garantia de 99,9% de disponibilidade](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/), ainda poderá incorrer em algum nível de período de indisponibilidade. Ao longo dos anos, o Stream Analytics introduziu métricas, registos e Estados de tarefas para refletir o estado de funcionamento das tarefas. Todos eles são apresentados através do serviço do Azure Monitor e podem ser exportados ainda mais ao OMS. Para obter mais informações, consulte [compreender o Stream Analytics da tarefa de monitorização e como monitorizar consultas](stream-analytics-monitoring.md).

![Monitorização do ASA](media/stream-analytics-solution-patterns/monitoring.png)

Existem duas coisas principais a monitorizar:

- [Estado de falha de tarefa](job-states.md)

    Primeiro e mais importante, tem de certificar-se de que a tarefa está em execução. Sem o trabalho em execução, não existem novas métricas ou registos são gerados. Tarefas que podem alterar para um Estado com falhas por vários motivos, incluindo a ter um elevado nível de utilização SU (ou seja, a ficar sem recursos).

- [Métricas de atraso de marca d'água](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Esta métrica reflete até que ponto por trás de seu processamento de pipeline está em tempo de relógio (segundos). Alguns do atraso é atribuída para a lógica de processamento inerente. Como resultado, a tendência de cada vez maior de monitorização é muito mais importante do que o valor absoluto de monitorização. O atraso de estado estável deverão ser tratado o design do aplicativo, não pela monitorização ou alertas.

Após a falha, registos de atividades e [registos de diagnóstico](stream-analytics-job-diagnostic-logs.md) são os melhores locais para começar a examinar a existência de erros.

## <a name="build-resilient-and-mission-critical-applications"></a>Compilação resiliente e aplicativos de missão crítica

Independentemente da garantia de SLA do Azure Stream Analytics e como cuidado executar a aplicação de ponto-a-ponto, as falhas ocorrem. Se seu aplicativo seja de missão crítica, precisa estar preparado para falhas para recuperar graciosamente.

Para alertar os aplicativos, a coisa mais importante é detectar o alerta seguinte. Pode optar por reiniciar a tarefa de atual ao recuperar, ignorando últimos alertas de tempo. A semântica de hora de início de tarefa é pela primeira vez de saída, não a primeira hora de entrada. A entrada é rewound com versões anteriores, uma quantidade apropriada de tempo para garantir que o primeiro resultado num momento especificado está completa e correta. Não obter agregações parciais e acionar alertas inesperadamente assim.

Também pode optar por iniciar saída a partir de determinado período de tempo no passado. Políticas de retenção de Hubs de eventos e do IoT Hub armazenar uma quantidade razoável de dados para permitir o processamento do passado. O compromisso é a velocidade pode acompanhar para a hora atual e começar a gerar alertas oportunos de novo. Dados perde rapidamente seu valor ao longo do tempo, pelo que é importante acompanhar rapidamente para a hora atual. Existem duas formas de acompanhar rapidamente:

- Aprovisione mais recursos (SU) quando ficando.
- Reinicie da hora atual.

O tempo reinicialização desde atual é simples de fazer, com o compromisso de deixar uma lacuna durante o processamento. Reiniciar desta forma pode ser OK para alertar os cenários, mas pode ser problemático para cenários de dashboard e é um não-starter de arquivamento e cenários de armazenamento de dados.

Mais recursos de aprovisionamento pode acelerar o processo, mas o efeito de ter um aumento da taxa de processamento é complexo.

- Teste que a tarefa é dimensionável para um número maior de SUs. Nem todas as consultas são dimensionáveis. Tem de certificar-se de que a consulta está [paralelizada](stream-analytics-parallelization.md).

- Certifique-se de que existem suficientes partições no IoT Hub que pode adicionar mais unidades de débito (n) para dimensionar o débito de entrada ou de Hubs de eventos a montante. Tenha em atenção que cada TU de Hubs de eventos maxes horizontalmente a uma taxa de saída de 2 MB/s.

- Certifique-se de que aprovisionou recursos suficientes no sinks de saída (ou seja, base de dados SQL, Cosmos DB), para que eles não limitar o aumento na saída, que por vezes, pode fazer com que o sistema bloquear a cópia de segurança.

A coisa mais importante é prever a alteração de taxa de processamento, testar esses cenários antes de entrar em produção e estar pronto para dimensionar o processamento corretamente durante o tempo de recuperação de falha.

No cenário extreme que eventos de entrada são todos os atrasada, [é possível todos os eventos são ignorados](stream-analytics-time-handling.md) se tiver aplicado uma janela que são recebida final para a sua tarefa. A eliminação de eventos pode parecer um comportamento misterioso no início; No entanto, Considerando o Stream Analytics é um motor de processamento em tempo real, ele espera eventos de entrada para ser próximo da hora de relógio de parede. Tem de remover os eventos que violam essas restrições.

### <a name="backfilling-process"></a>Processo de preenchimento

Felizmente, o padrão de arquivamento de dados anterior pode ser utilizado para processar estes eventos atrasados corretamente. A idéia é que a tarefa de arquivamento processa eventos recebidos em tempo de chegada e arquivos mortos de eventos locais para o registo de momento certo no Blob do Azure ou do Azure Data Lake Store com o seu tempo de eventos. Não importa como tarde chega um evento, ele nunca será ignorado. Ele sempre será direcionado no bucket de momento certo. Durante a recuperação, é possível reprocessar os eventos arquivados e o preenchimento os resultados para o armazenamento de eleição.

![Preenchimento do ASA](media/stream-analytics-solution-patterns/backfill.png)

O processo de preenchimento tem de ser feito com um sistema, o que provavelmente tem um modelo de programação diferente do Azure Stream Analytics de processamento em lotes offline. Isso significa que precisa implementar a lógica de processamento inteiro novamente.

Para o preenchimento, ainda é importante para pelo menos temporariamente aprovisionar que Coletores de mais recursos para a saída para processar um débito mais elevado do que o estado de repouso necessidades de processamento.

|Cenários  |Reiniciado a partir de agora apenas  |Reiniciado a partir de última hora de paragem |Reiniciado a partir de agora + preenchimento com eventos arquivados|
|---------|---------|---------|---------|
|**Dashboarding**   |Cria a lacuna    |OK para breve interrupção    |Utilize para falha de tempo |
|**Alertas**   |Aceitável |OK para breve interrupção    |Não é necessário |
|**Aplicação de fornecimento de eventos** |Aceitável |OK para breve interrupção    |Utilize para falha de tempo |
|**Armazém de dados**   |Perda de dados  |Aceitável |Não é necessário |
|**Análise offline**  |Perda de dados  |Aceitável |Não é necessário|

## <a name="putting-it-all-together"></a>Juntar tudo

Não é difícil imaginar que todos os padrões de solução mencionados acima podem ser combinados num sistema complexo de ponto-a-ponto. O sistema combinado pode incluir dashboards, alertas, aplicação de fornecimento de eventos, armazenamento de dados e capacidades de análise offline.

A chave é crie o seu sistema em padrões compostos, para que cada subsistema pode ser criado, testadas, atualizado e recuperar de forma independente.

## <a name="next-steps"></a>Passos Seguintes

Agora viu uma variedade de padrões de solução com o Azure Stream Analytics. Em seguida, pode criar o seu primeiro trabalho do Stream Analytics e experimentá-lo na prática:

* [Create a Stream Analytics job by using the Azure portal](stream-analytics-quick-create-portal.md) (Criar um trabalho do Stream Analytics com o portal do Azure).
* [Create a Stream Analytics job by using Azure PowerShell](stream-analytics-quick-create-powershell.md) (Criar um trabalho do Stream Analytics com o Azure PowerShell).
* [Criar uma tarefa do Stream Analytics com o Visual Studio](stream-analytics-quick-create-vs.md).
