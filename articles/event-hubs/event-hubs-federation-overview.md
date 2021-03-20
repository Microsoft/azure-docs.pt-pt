---
title: Federação multi-local e multi-região - Azure Event Hubs | Microsoft Docs
description: Este artigo fornece uma visão geral da federação multi-site e multi-região com Azure Event Hubs.
ms.topic: article
ms.date: 12/12/2020
ms.author: spelluru
ms.openlocfilehash: 12ef895c8b16fe18ed02ebf01d17624ac71c2f3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97861456"
---
# <a name="multi-site-and-multi-region-federation"></a>Federação de vários sites e várias regiões

Muitas soluções sofisticadas exigem que os mesmos fluxos de eventos sejam disponibilizados para consumo em vários locais, ou exigem que os fluxos de eventos sejam recolhidos em vários locais e depois consolidados num local específico para consumo. Há também muitas vezes a necessidade de enriquecer ou reduzir fluxos de eventos ou fazer conversões de formato de evento, também para dentro de uma única região e solução.

Na prática, isso significa que a sua solução irá manter múltiplos Centros de Eventos, muitas vezes em diferentes regiões e espaços de nomes de Event Hubs, e depois replicar eventos entre eles. Você também pode trocar eventos com fontes e alvos como [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md), [Azure IoT Hub](../iot-fundamentals/iot-introduction.md), ou Apache [Kafka](https://kafka.apache.org). 

A manutenção de múltiplos Centros de Eventos ativos em diferentes regiões também permite que os clientes escolham e troquem entre eles se os seus conteúdos estiverem a ser fundidos, o que torna o sistema global mais resistente às questões de disponibilidade regional.

Este capítulo "Federação" explica os padrões da federação e como realizar estes padrões utilizando o [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) sem servidor ou os tempos de funcionamento das [Funções Azure,][1] com a opção de ter o seu próprio código de transformação ou enriquecimento no caminho de fluxo do evento. 

## <a name="federation-patterns"></a>Padrões da Federação

Existem muitas motivações potenciais para o porquê de você querer mover eventos entre diferentes Centros de Eventos ou outras fontes e alvos, e enumeramos os padrões mais importantes nesta secção e também associamos a orientações mais detalhadas para o respetivo padrão. 

- [Resiliência contra eventos de disponibilidade regional](#resiliency-against-regional-availability-events)
- [Otimização da latência](#latency-optimization)
- [Validação, redução e enriquecimento](#validation-reduction-and-enrichment)
- [Integração com serviços de análise](#integration-with-analytics-services)
- [Consolidação e normalização dos fluxos de eventos](#consolidation-and-normalization-of-event-streams)
- [Divisão e encaminhamento de fluxos de eventos](#splitting-and-routing-of-event-streams)
- [Projeções de registo](#log-projections)
  
### <a name="resiliency-against-regional-availability-events"></a>Resiliência contra eventos de disponibilidade regional 

![Disponibilidade Regional](media/event-hubs-federation-overview/regional-availability.jpg)

Embora a máxima disponibilidade e fiabilidade sejam as principais prioridades operacionais para os Centros de Eventos, existem, no entanto, muitas formas pelas quais um produtor ou consumidor pode ser impedido de falar com o seu "principal" Centro de Eventos devido a problemas de networking ou resolução de nomes, ou quando um Centro de Eventos pode, de facto, não responder temporariamente ou retornar erros. 

Tais condições não são "desastrosas" de tal forma que você vai querer abandonar completamente o destacamento regional como você poderia fazer em uma situação de [recuperação](event-hubs-geo-dr.md) de desastres, mas o cenário de negócio de algumas aplicações pode já ser impactado por eventos de disponibilidade que não duram mais do que alguns minutos ou mesmo segundos. 

Existem dois padrões fundamentais para abordar tais cenários:

- O padrão de [replicação][4] é sobre replicar o conteúdo de um Centro de Eventos primário para um Centro de Eventos secundário, pelo que o principal Centro de Eventos é geralmente utilizado pela aplicação para eventos de produção e consumo e o secundário serve como uma opção de retorno no caso de o Centro de Eventos primário se tornar indisponível. Uma vez que a replicação é unidirecional, desde o primário ao secundário, uma transição tanto dos produtores como dos consumidores de uma primária indisponível para a secundária fará com que a antiga primária não receba mais novos eventos, pelo que deixará de ser atual.
  A replicação pura é, portanto, apenas adequada para cenários de failover unidirecionais. Uma vez realizada a falha, a antiga primária é abandonada e um novo Centro de Eventos secundário precisa de ser criado numa região alvo diferente.
- O padrão [de fusão][5] estende o padrão de replicação através da realização de uma fusão contínua do conteúdo de dois ou mais Centros de Eventos. Cada evento originalmente produzido num dos Centros de Eventos incluídos no esquema é replicado para os outros Centros de Eventos. À medida que os eventos são replicados, são anotados de forma a serem subsequentemente ignorados pelo processo de replicação do alvo de replicação. Os resultados da utilização do padrão de fusão são dois ou mais Centros de Eventos que conterão o mesmo conjunto de eventos de uma forma eventualmente consistente. 
  
Em qualquer dos casos, o conteúdo dos Centros de Eventos não será idêntico. Os acontecimentos de qualquer produtor e agrupados pela mesma chave de partição aparecerão na mesma ordem relativa que inicialmente apresentado, mas a ordem absoluta dos acontecimentos pode diferir. Isto é especialmente verdade para cenários onde a contagem de partição de fontes e centros de eventos alvo diferem, o que é desejável para vários dos padrões estendidos descritos aqui. Um [divisória ou router](#splitting-and-routing-of-event-streams) pode obter uma fatia de um Centro de Eventos muito maior com centenas de divisórias e funil em um pequeno Centro de Eventos com apenas um punhado de divisórias, mais adequado para manusear o subconjunto com recursos de processamento limitados. Inversamente, uma [consolidação](#consolidation-and-normalization-of-event-streams) pode funilizar dados de vários pequenos Centros de Eventos para um único e maior Centro de Eventos com mais divisórias para lidar com as necessidades consolidadas de produção e processamento.

O critério para manter os eventos juntos é a chave de partição e não o ID original da partição. Outras considerações sobre a ordem relativa e como executar uma falha de um Event Hub para o próximo sem depender do mesmo âmbito de compensações de fluxo é discutida na descrição do padrão [de replicação.][4]


Orientação: 
- [Padrão de replicação][4]
- [Padrão de fusão][5]

### <a name="latency-optimization"></a>Otimização da latência 

![Otimização da Latência](media/event-hubs-federation-overview/latency-optimization.jpg)  

Os streams de eventos são escritos uma vez pelos produtores, mas podem ser lidos várias vezes pelos consumidores do evento. Para cenários em que um fluxo de eventos numa região é partilhado por vários consumidores, e precisa de ser acedido repetidamente durante o processamento de análises residentes numa região diferente, ou com todas as exigências que deixariam os consumidores simultâneos à fome, pode ser benéfico colocar uma cópia do fluxo de eventos perto do processador de análise para reduzir a latência de ida e volta. 

Bons exemplos para quando a replicação deve ser preferível ao consumir eventos remotamente de todas as regiões são especialmente aqueles onde as regiões estão extremamente distantes, por exemplo, a Europa e a Austrália sendo quase antípodas, geograficamente e atrasos de rede podem facilmente ultrapassar os 250 ms para qualquer viagem de ida e volta.
Não se pode acelerar a velocidade da luz, mas pode reduzir o número de viagens de ida e volta de alta latência para interagir com os dados.

Orientação: 
- [Padrão de replicação][4]

### <a name="validation-reduction-and-enrichment"></a>Validação, redução e enriquecimento

![Validação, redução, enriquecimento](media/event-hubs-federation-overview/validation-enrichment.jpg)  

Os streams de eventos podem ser submetidos a um Centro de Eventos por clientes externos à sua própria solução. Tais fluxos de eventos podem exigir que os eventos submetidos externamente sejam verificados para o cumprimento de um determinado esquema, e para que os eventos não conformes sejam eliminados. 

Em cenários em que os clientes são extremamente limitados pela largura de banda, como acontece em muitos cenários de "Internet das Coisas" com largura de banda medido, ou onde os eventos são originalmente enviados através de redes não IP com tamanhos de pacotes limitados, os eventos podem ter de ser enriquecidos com dados de referência para adicionar mais contexto para serem utilizáveis por processadores de eventos a jusante.

Noutros casos, especialmente quando os fluxos estão a ser consolidados, os dados do evento podem ter de ser reduzidos em complexidade ou dimensão pura, omitindo alguns detalhes.

Qualquer uma destas operações pode ocorrer como parte de fluxos de replicação, consolidação ou fusão. 

Orientação: 
- [Padrão de editor][6]

### <a name="integration-with-analytics-services"></a>Integração com serviços de análise

![Integração com serviços de análise](media/event-hubs-federation-overview/integration.jpg)

Vários dos serviços de análise nativas de nuvem da Azure, como o Azure Stream Analytics ou o Azure Synapse, funcionam melhor com dados transmitidos ou pré-lotados servidos a partir de Azure Event Hubs, e o Azure Event Hubs também permite a integração com vários pacotes de análise de código aberto, tais como Apache Samza, Apache Flink, Apache Spark e Apache Storm. 

Se a sua solução utilizar principalmente Service Bus ou Event Grid, pode tornar estes eventos facilmente acessíveis a tais sistemas de análise e também para arquivo com Event Hubs Capture se os canalizar para o Event Hub. A Event Grid pode fazê-lo de forma nativa com a sua [integração event Hub,](../event-grid/handler-event-hubs.md)para o Service Bus siga a [orientação de replicação](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub)do Service Bus.

O Azure Stream Analytics [integra-se diretamente com os Centros de Eventos.](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs)

Orientação: 
- [Padrão de replicação][4]

### <a name="consolidation-and-normalization-of-event-streams"></a>Consolidação e normalização dos fluxos de eventos

![Consolidação e normalização dos fluxos de eventos](media/event-hubs-federation-overview/consolidation.jpg)

As soluções globais são muitas vezes compostas por pegadas regionais que são largamente independentes, incluindo ter as suas próprias capacidades de análise, mas as perspetivas de análise supranando e globais exigirão uma perspetiva integrada e é por isso que uma consolidação central dos mesmos fluxos de eventos que são avaliados nas respetivas pegadas regionais para a perspetiva local. 

A normalização é um sabor do cenário de consolidação, pelo que dois ou mais fluxos de eventos de entrada carregam o mesmo tipo de eventos, mas com diferentes estruturas ou diferentes codificações, e os eventos mais são transcodificados ou transformados antes de poderem ser consumidos. 

A normalização também pode incluir trabalhos criptográficos, tais como desencriptação de cargas encriptadas de ponta a ponta e reencrimar com diferentes teclas e algoritmos para o público do consumidor a jusante. 

Orientação: 
- [Padrão de fusão][5]
- [Padrão de editor][6]

### <a name="splitting-and-routing-of-event-streams"></a>Divisão e encaminhamento de fluxos de eventos

![Divisão e encaminhamento de fluxos de eventos](media/event-hubs-federation-overview/splitting.jpg)

O Azure Event Hubs é ocasionalmente utilizado em cenários de estilo de "publicação-subscrição" onde uma torrente de eventos ingeridos excede em muito a capacidade da Azure Service Bus ou da Azure Event Grid, ambas com capacidades nativas de filtragem e distribuição de subscrição e são preferíveis para este padrão. 

Embora uma verdadeira capacidade de "publicar-subscrever" deixe aos assinantes a escolha dos eventos que querem, o padrão de divisão tem os eventos do mapa do produtor para divisórias por um modelo de distribuição pré-determinado e os consumidores designados, em seguida, exclusivamente puxar da "sua" partição. Com o Event Hub a tamponar o tráfego global, o conteúdo de uma determinada partição, representando uma fração do volume de produção original, pode então ser replicado numa fila para consumo fiável, transacional e concorrente.

Muitos cenários em que o Event Hubs é usado principalmente para eventos de mudança dentro de uma aplicação dentro de uma região têm alguns casos em que eventos selecionados, talvez apenas a partir de uma única partição, também têm de ser disponibilizados em outros lugares. Este cenário é semelhante ao cenário de divisão, mas pode usar um router escalável que considera todas as mensagens que chegam num Event Hub e escolhe apenas algumas para o encaminhamento em diante e pode diferenciar alvos de encaminhamento por metadados ou conteúdo de eventos. 

Orientação:
- [Padrão de encaminhamento][7]

### <a name="log-projections"></a>Projeções de registo 

![Projeção de registo](media/event-hubs-federation-overview/log-projection.jpg)

Em alguns cenários, você vai querer ter acesso ao valor mais recente enviado para qualquer substream de um evento, e geralmente distinguido pela chave de partição. Em Apache Kafka, isso é muitas vezes conseguido permitindo a "compactação de registo" sobre um tópico, que descarta todos, menos o mais recente evento rotulado com qualquer chave única. A abordagem de compactação de registos tem três desvantagens compostas: 

- A compactação requer uma reorganização contínua do log, que é uma operação excessivamente cara para um corretor que é otimizado para cargas de trabalho apenas apêndice. 
- A compactação é destrutiva e não permite uma perspetiva compactada e não compactada do mesmo fluxo.
- Um fluxo compactado ainda tem um modelo de acesso sequencial, o que significa que encontrar o valor desejado no log requer a leitura de todo o log no pior dos casos, o que normalmente leva a otimizações que implementam o padrão exato apresentado aqui: projetar o conteúdo do registo em uma base de dados ou cache. 

Em última análise, um log compactado é uma loja de valor-chave e, como tal, é a pior opção de implementação possível para tal loja. É muito mais eficiente para pesquisas e para consultas para criar e usar uma projeção permanente do log em uma loja de valor-chave adequada ou qualquer outra base de dados. 

Uma vez que os eventos são imutáveis e a ordem é sempre preservada num log, qualquer projeção de um log em uma loja de valor-chave será sempre idêntica para a mesma gama de eventos, o que significa que uma projeção que você mantém atualizada sempre fornece uma visão autoritária e nunca há nenhuma boa razão para reconstruí-lo a partir do conteúdo de log uma vez construído. 

Orientação:
- [Projeção de registo][8]

## <a name="replication-application-technologies"></a>Tecnologias de aplicação de replicação

A implementação dos padrões acima requer um ambiente de execução escalável e fiável para as tarefas de replicação que pretende configurar e executar. No Azure, os ambientes de tempo de execução mais adequados para tais tarefas são tarefas apátridas são [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) para tarefas de replicação de fluxos e [funções Azure](../azure-functions/functions-overview.md) para tarefas de replicação apátrida.

### <a name="stateful-replication-applications-in-azure-stream-analytics"></a>Aplicações de replicação imponentes no Azure Stream Analytics

Para aplicações de replicação imponente que precisam considerar relações entre eventos, criar eventos compósitos, enriquecer eventos ou reduzir eventos, criar agregações de dados e transformar cargas de eventos, o [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) é a melhor opção de implementação.

No Azure Stream Analytics, [cria-se empregos](../stream-analytics/stream-analytics-quick-create-portal.md) que integram [entradas](../stream-analytics/stream-analytics-add-inputs.md) e saídas e integra os [dados](../stream-analytics/stream-analytics-define-outputs.md) a partir das entradas através de [consultas](/stream-analytics-query/stream-analytics-query-language-reference) que produzem um resultado que é depois disponibilizado nas saídas.

As consultas são baseadas na [linguagem de consulta SQL](/stream-analytics-query/stream-analytics-query-language-reference) e podem ser usadas para filtrar, classificar, agregar e juntar dados de streaming durante um período de tempo. Também pode estender este idioma SQL com funções definidas pelo utilizador [JavaScript](../stream-analytics/stream-analytics-javascript-user-defined-functions.md) e [C# (UDFs)](../stream-analytics/stream-analytics-edge-csharp-udf-methods.md). Pode ajustar facilmente as opções de encomenda de eventos e a duração das janelas de tempo ao realizar operações de agregação através de simples construções e/ou configurações linguísticas.

Cada trabalho tem uma ou várias saídas para os dados transformados, e você pode controlar o que acontece em resposta à informação que analisou. Pode, por exemplo:

- Envie dados para serviços como Azure Functions, Service Bus Topics ou Queues para desencadear comunicações ou fluxos de trabalho personalizados a jusante.
- Envie dados para um dashboard Power BI para fazer dashboards em tempo real.
- Armazenar dados em outros serviços de armazenamento Azure (por exemplo, Azure Data Lake, Azure Synapse Analytics, etc.) para realizar analíticos de lotes ou modelos de aprendizagem de máquinas de comboio com base em piscinas muito grandes e indexadas de dados históricos.
- Armazenar projeções (também chamadas de "vistas materializadas") em bases de dados[(SQL Database,](../stream-analytics/sql-database-output.md) [Cosmos DB).](../stream-analytics/azure-cosmos-db-output.md)

### <a name="stateless-replication-applications-in-azure-functions"></a>Aplicações de replicação apátridas em Funções Azure

Para tarefas de replicação apátridas em que pretende encaminhar eventos sem considerar as suas cargas ou processá-los sem ter de considerar as relações dos acontecimentos (exceto a sua ordem relativa), pode utilizar as Funções Azure, que proporcionam uma enorme flexibilidade.

A Azure Functions tem gatilhos pré-construídos, escaláveis e encadernações de saída para [Azure Event Hubs](../azure-functions/functions-bindings-event-hubs.md), [Azure IoT Hub,](../azure-functions/functions-bindings-event-iot.md) [Azure Service Bus,](../azure-functions/functions-bindings-service-bus.md) [Azure Event Grid](../azure-functions/functions-bindings-event-grid.md), e [Azure Queue Storage,](../azure-functions/functions-bindings-storage-queue.md)bem como extensões personalizadas para [RabbitMQ,](https://github.com/azure/azure-functions-rabbitmq-extension)e [Apache Kafka.](https://github.com/azure/azure-functions-kafka-extension) A maioria dos gatilhos adaptar-se-á dinamicamente às necessidades de produção, escalando o número de instâncias de execução simultânea para cima e para baixo com base em métricas documentadas. 

Para projeções de registos de construção, a Azure Functions suporta encadernações de saída para [o Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-output.md) e [para o Armazenamento de Mesas Azure](../azure-functions/functions-bindings-storage-table-output.md).

As Funções Azure podem funcionar sob uma identidade gerida pelo [Azure](../active-directory/managed-identities-azure-resources/overview.md) e, com isso, pode manter os valores de configuração para credenciais em armazenamento controlado de acesso apertado dentro do Cofre da [Chave Azure](../key-vault/general/overview.md).

Além disso, as Funções Azure permitem que as tarefas de replicação se integrem diretamente com redes virtuais Azure e [pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço para todos os serviços de mensagens Azure, e é facilmente integrado com [o Azure Monitor.](../azure-monitor/overview.md)

Com o plano de consumo de Funções Azure, os gatilhos pré-construídos podem até zero enquanto não há mensagens disponíveis para replicação, o que significa que não incorre em custos para manter a configuração pronta a escalar de novo; a principal desvantagem da utilização do plano de consumo é que a latência para tarefas de replicação "acordar" deste Estado é significativamente maior do que com os planos de hospedagem onde a infraestrutura é mantida em funcionamento.  

Em contraste com tudo isto, os motores de replicação mais comuns para mensagens e eventos, como [o MirrorMaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker) da Apache Kafka, exigem que você forneça um ambiente de hospedagem e escala o próprio motor de replicação. Isto inclui configurar e integrar as funcionalidades de segurança e networking e facilitar o fluxo de dados de monitorização, e então ainda não tem a oportunidade de injetar tarefas de replicação personalizadas no fluxo. 

### <a name="choosing-between-azure-functions-and-azure-stream-analytics"></a>Escolher entre funções Azure e Azure Stream Analytics

O Azure Stream Analytics (ASA) é a melhor opção sempre que precisa de processar a carga útil dos seus eventos enquanto os replica. A ASA pode copiar eventos um a um ou pode criar agregados que condensam a informação dos fluxos de eventos antes de reencaminhar. Pode [facilmente apoiar-se em complementar os dados de referência retidos](../stream-analytics/stream-analytics-use-reference-data.md) no Azure Blob Storage ou na Azure SQL Database sem ter de importar esses dados para um fluxo.

Com a ASA, pode facilmente criar vistas persistentes e materializadas de fluxos em bases de dados de hiperescala. É uma abordagem muito superior ao modelo desajeitado de "log compaction" de Apache Kafka e às projeções voláteis da mesa do lado do cliente de Kafka Streams. 

A ASA pode facilmente processar eventos com cargas explosivas codificadas nos [formatos CSV, JSON e Apache Avro](../stream-analytics/stream-analytics-parsing-json.md) e pode ligar [os deserializadores personalizados](../stream-analytics/custom-deserializer.md) para qualquer outro formato.

Para todas as tarefas de replicação em que pretende copiar streams de eventos "as-is" e sem tocar nas cargas, ou se precisar de implementar um router, realizar trabalhos criptográficos, alterar a codificação de cargas útil, ou se de outra forma precisar de controlo total sobre o conteúdo do fluxo de dados, a Azure Functions é a melhor opção.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, exploramos uma série de padrões de federação e explicamos o papel das Funções Azure como o evento e o tempo de replicação de mensagens em Azure.

Em seguida, você pode querer ler como configurar uma aplicação de replicador com Azure Stream Analytics ou Azure Functions e, em seguida, como replicar fluxos de eventos entre Os Centros de Eventos e vários outros sistemas de eventos e mensagens:

- [Padrões da tarefa de replicação de eventos][10]
- [Dados do processo com a Azure Stream Analytics][9]
- [Aplicações de replicador de eventos em Funções Azure][1]
- [Replicação de eventos entre centros de eventos][2]
- [Replicando eventos para a Azure Service Bus][3]
- [Use Apache Kafka MirrorMaker com centros de eventos][11] 

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
[4]: event-hubs-federation-patterns.md#replication
[5]: event-hubs-federation-patterns.md#merge
[6]: event-hubs-federation-patterns.md#editor
[7]: event-hubs-federation-patterns.md#routing
[8]: event-hubs-federation-patterns.md#log-projection
[9]: process-data-azure-stream-analytics.md
[10]: event-hubs-federation-patterns.md#replication
[11]: event-hubs-kafka-mirror-maker-tutorial.md