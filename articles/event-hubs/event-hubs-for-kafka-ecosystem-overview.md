---
title: Use o centro de eventos da aplicação Apache Kafka - Azure Event Hubs ! Microsoft Docs
description: Este artigo fornece informações sobre o suporte da Apache Kafka pela Azure Event Hubs.
ms.topic: article
ms.date: 09/25/2020
ms.openlocfilehash: 2b101adf173f3d623bb85d811ba5832020313f14
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92327302"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Use hubs de eventos Azure a partir de aplicações Apache Kafka
O Event Hubs fornece um ponto final compatível com as APIs de produtor e consumidor apache kafka ® que podem ser usadas pela maioria das aplicações de clientes Apache Kafka existentes como alternativa à gestão do seu próprio cluster Apache Kafka. O Event Hubs apoia os clientes APIs de produtor e consumidor da Apache Kafka na versão 1.0 ou superior.

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>O que os Centros de Eventos de Kafka fornecem?

O evento Hubs for Apache Kafka apresenta uma cabeça de protocolo em cima dos Azure Event Hubs que é compatível com os clientes Apache Kafka construídos para as versões de servidor Apache Kafka 1.0 e posteriormente e suporta tanto a leitura de ambos os Centros de Eventos, que são equivalentes aos tópicos apache kafka. 

Pode frequentemente utilizar o ponto final do Event Hubs Kafka a partir das suas aplicações sem alterações de código em comparação com a configuração existente de Kafka e apenas modificar a configuração: Atualize a cadeia de ligação em configurações para apontar para o ponto final kafka exposto pelo seu centro de eventos em vez de apontar para o seu cluster Kafka. Em seguida, pode começar a transmitir eventos a partir das suas aplicações que usam o protocolo Kafka em Event Hubs. 

Conceptualmente, os Centros de Kafka e Eventos são muito semelhantes: são ambos registos divididos construídos para dados de streaming, pelo que o cliente controla qual parte do registo retido que quer ler. Os seguintes conceitos de mapas de mesa entre Kafka e Event Hubs.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Mapeamento conceptual kafka e Event Hub

| Conceito kafka | Conceito de hubs de eventos|
| --- | --- |
| Cluster | Espaço de Nomes |
| Tópico | Hub de Eventos |
| Partição | Partição|
| Grupo de Consumidores | Grupo de Consumidores |
| Desvio | Desvio|

### <a name="key-differences-between-apache-kafka-and-event-hubs"></a>Principais diferenças entre Apache Kafka e Centros de Eventos

Enquanto [o Apache Kafka](https://kafka.apache.org/) é um software que normalmente precisa para instalar e operar, o Event Hubs é um serviço totalmente gerido e nativo da nuvem. Não existem servidores, discos ou redes para gerir e monitorizar e nenhum corretor a considerar ou configurar, nunca. Você cria um espaço de nome, que é um ponto final com um nome de domínio totalmente qualificado, e então você cria Centros de Evento (tópicos) dentro desse espaço de nome. 

Para obter mais informações sobre os Centros de Eventos e espaços de nome, consulte [as funcionalidades do Event Hubs](event-hubs-features.md#namespace). Como serviço na nuvem, o Event Hubs utiliza um único endereço IP virtual estável como ponto final, para que os clientes não precisem de saber sobre os corretores ou máquinas dentro de um cluster. Mesmo que o Event Hubs implemente o mesmo protocolo, esta diferença significa que todo o tráfego kafka para todas as divisórias é previsivelmente encaminhado através deste ponto final em vez de exigir acesso de firewall para todos os corretores de um cluster.   

A escala em Event Hubs é controlada por quantas unidades de produção você compra, com cada unidade de produção que lhe dá 1 Megabyte por segundo, ou 1000 eventos por segundo de entrada e o dobro desse volume em saída. Os Centros de Eventos podem escalar automaticamente as unidades de produção quando atingir o limite de saída se utilizar a função [Auto-Insuflado;](event-hubs-auto-inflate.md) este trabalho de recurso também funciona com o suporte do protocolo Apache Kafka.  

### <a name="is-apache-kafka-the-right-solution-for-your-workload"></a>Apache Kafka é a solução certa para a sua carga de trabalho?

A partir de aplicações de construção usando Apache Kafka, também será útil entender que a Azure Event Hubs faz parte de uma frota de serviços que também inclui [a Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md), e a [Azure Event Grid.](../event-grid/overview.md) 

Embora alguns fornecedores de distribuição comercial da Apache Kafka possam sugerir que a Apache Kafka é um balcão único para todas as necessidades da sua plataforma de mensagens, a realidade é que a Apache Kafka não implementa, por exemplo, o padrão de fila [concorrente do consumidor,](/azure/architecture/patterns/competing-consumers) não tem suporte para  [publicar-subscrever](/azure/architecture/patterns/publisher-subscriber) a um nível que permita aos assinantes aceder às mensagens recebidas com base em regras avaliadas pelo servidor que não sejam compensações simples, e não tem instalações para acompanhar o ciclo de vida de um trabalho iniciado por uma mensagem ou lateralmente de mensagens defeituosas numa fila de letras mortas, todas elas fundamentais para muitas empresas.

Para entender as diferenças entre padrões e qual o padrão melhor coberto por qual serviço, por favor reveja as [opções de mensagens Assíncronas na orientação Azure.](/azure/architecture/guide/technology-choices/messaging) Como utilizador da Apache Kafka, poderá descobrir que os caminhos de comunicação que até agora realizou com a Kafka, podem ser realizados com uma complexidade muito menos básica e ainda mais potentes, utilizando tanto a Grade de Eventos como o Service Bus. 

Se precisar de características específicas do Apache Kafka que não estão disponíveis através dos Centros de Eventos para a interface Apache Kafka ou se o seu padrão de implementação exceder as quotas do [Event Hubs,](event-hubs-quotas.md)também pode executar um [cluster Apache Kafka nativo em Azure HDInsight.](../hdinsight/kafka/apache-kafka-introduction.md)  

## <a name="security-and-authentication"></a>Segurança e autenticação
Sempre que publica ou consome eventos a partir de um Centro de Eventos para a Kafka, o seu cliente está a tentar aceder aos recursos do Event Hubs. Pretende assegurar que os recursos são acedidos através de uma entidade autorizada. Ao utilizar o protocolo Apache Kafka com os seus clientes, pode definir a sua configuração para autenticação e encriptação utilizando os mecanismos SASL. Ao utilizar os Centros de Eventos para Kafka requer a encriptação TLS (uma vez que todos os dados em trânsito com os Centros de Eventos são encriptados por TLS). Pode ser feito especificando a opção SASL_SSL no seu ficheiro de configuração. 

O Azure Event Hubs oferece múltiplas opções para autorizar o acesso aos seus recursos seguros. 

- OAuth 2.0
- Assinatura de acesso partilhado (SAS)

#### <a name="oauth-20"></a>OAuth 2.0
O Event Hubs integra-se com o Azure Ative Directory (Azure AD), que fornece um servidor de autorização centralizado compatível com **OAuth 2.0.** Com a Azure AD, pode utilizar o controlo de acesso baseado em funções (Azure RBAC) para conceder permissões finas às identidades do seu cliente. Pode utilizar esta funcionalidade com os seus clientes Kafka especificando **SASL_SSL** para o protocolo e  **OAUTHBEARER** para o mecanismo. Para obter mais informações sobre as funções e níveis de Azure para o acesso à deteção, consulte [o Acesso autorizado com a Azure AD](authorize-access-azure-active-directory.md).

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Assinatura de acesso compartilhado (SAS)
O Event Hubs também fornece as **Assinaturas de Acesso Compartilhado (SAS)** para acesso delegado aos Centros de Eventos para recursos kafka. Autorizar o acesso utilizando o mecanismo baseado em fichas OAuth 2.0 proporciona uma segurança superior e facilidade de utilização sobre o SAS. As funções incorporadas também podem eliminar a necessidade de autorização baseada em ACL, que tem de ser mantida e gerida pelo utilizador. Pode utilizar esta funcionalidade com os seus clientes Kafka especificando **SASL_SSL** para o protocolo e **a PLAIN** para o mecanismo. 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!NOTE]
> Ao utilizar a autenticação SAS com clientes Kafka, as ligações estabelecidas não são desligadas quando a tecla SAS é regenerada. 

#### <a name="samples"></a>Amostras 
Para um **tutorial** com instruções passo a passo para criar um centro de eventos e acessá-lo usando SAS ou OAuth, consulte [Quickstart: Streaming de dados com Centros de Eventos utilizando o protocolo Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Para mais **amostras** que mostrem como usar o OAuth com Os Centros de Eventos para Kafka, consulte [as amostras no GitHub.](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)

## <a name="other-event-hubs-features"></a>Outras funcionalidades do Event Hubs 

O evento Hubs para Apache Kafka é um dos três protocolos disponíveis simultaneamente nos Azure Event Hubs, complementando HTTP e AMQP. Pode escrever com qualquer um destes protocolos e ler com qualquer outro, para que os seus atuais produtores Apache Kafka possam continuar a publicar através da Apache Kafka, mas o seu leitor pode beneficiar da integração nativa com a interface AMQP do Event Hubs, como a Azure Stream Analytics ou Azure Functions. Inversamente, pode facilmente integrar os Hubs de Eventos Azure em redes de encaminhamento AMQP como ponto final alvo, e ainda ler dados através das integrações apache Kafka.  

Além disso, funcionalidades de Event Hubs como [Capture](event-hubs-capture-overview.md), que permite um arquivo de longo prazo extremamente eficiente em termos de custos através do armazenamento de Azure Blob e do Armazenamento do Lago de Dados Azure, e [geo-recuperação de desastres](event-hubs-geo-dr.md) também trabalham com os Centros de Eventos para a funcionalidade Kafka.

## <a name="apache-kafka-feature-differences"></a>Apache Kafka apresenta diferenças 

O objetivo do Event Hubs para Apache Kafka é fornecer acesso às capacidades do Azure Event Hub para aplicações que estão bloqueadas na API Apache Kafka e que de outra forma teriam de ser apoiadas por um cluster Apache Kafka. 

Como [explicado acima,](#is-apache-kafka-the-right-solution-for-your-workload)a frota de mensagens Azure oferece uma cobertura rica e robusta para uma multiplicidade de cenários de mensagens, e embora as seguintes funcionalidades não sejam atualmente suportadas através do suporte do Event Hubs para a API Apache Kafka, apontamos onde e como a capacidade desejada está disponível.

### <a name="transactions"></a>Transações

[A Azure Service Bus](../service-bus-messaging/service-bus-transactions.md) tem um suporte de transação robusto que permite receber e acomodar mensagens e sessões ao mesmo tempo que envia mensagens de saída resultantes do processamento de mensagens a várias entidades-alvo sob a proteção de consistência de uma transação. O conjunto de funcionalidades não só permite o processamento exatamente uma vez de cada mensagem numa sequência, como também evita o risco de outro consumidor reprocessar inadvertidamente as mesmas mensagens que seria o caso de Apache Kafka. O Service Bus é o serviço recomendado para cargas de trabalho de mensagens transacionais.

### <a name="compression"></a>Compressão

A função de [compressão](https://cwiki.apache.org/confluence/display/KAFKA/Compression) do lado do cliente da Apache Kafka comprime um lote de várias mensagens numa única mensagem do lado do produtor e descomprime o lote do lado do consumidor. O corretor Apache Kafka trata o lote como uma mensagem especial.

Esta funcionalidade está fundamentalmente em desacordo com o modelo multi-protocolo do Azure Event Hubs, que permite que as mensagens, mesmo as enviadas em lotes, sejam extra-obtidas individualmente do corretor e através de qualquer protocolo. 

A carga útil de qualquer evento event Hub é um byte stream e o conteúdo pode ser comprimido com um algoritmo à sua escolha. O formato de codificação Apache Avro suporta a compressão de forma nativa.

### <a name="log-compaction"></a>Compaction de registo

A compactação de log Apache Kafka é uma característica que permite despejar todos, menos o último registo de cada chave de uma partição, que efetivamente transforma um tópico Apache Kafka numa loja de valor-chave onde o último valor acrescentado se sobrepõe à anterior. O padrão de loja de valor-chave, mesmo com atualizações frequentes, é muito melhor suportado por serviços de base de dados como [o Azure Cosmos DB](../cosmos-db/introduction.md).

A função de compactação de registos é utilizada pelas estruturas do cliente Kafka Connect e Kafka Streams.

### <a name="kafka-streams"></a>Riachos Kafka

Kafka Streams é uma biblioteca de clientes para análise de stream que faz parte do projeto Apache Kafka open source, mas é separada do corretor de stream de eventos Apache Kafka. 

A razão mais comum para os clientes do Azure Event Hubs pedirem apoio à Kafka Streams é porque estão interessados no produto "ksqlDB" da Confluent. "ksqlDB" é um projeto de fonte partilhada proprietário que é [licenciado de modo](https://github.com/confluentinc/ksql/blob/master/LICENSE) a que nenhum fornecedor "ofereça software-as-a-service, plataforma-as-a-service, infra-estruturas-as-a-service ou outros serviços online similares que competem com produtos ou serviços Confluent" é autorizado a usar ou oferecer suporte "ksqlDB". Na prática, se utilizar o ksqlDB, deve operar o próprio Kafka ou deve utilizar as ofertas em nuvem da Confluent. As condições de licenciamento também podem afetar os clientes da Azure que oferecem serviços para um propósito excluído pela licença.

Autónomo e sem ksqlDB, a Kafka Streams tem menos capacidades do que muitos quadros e serviços alternativos, a maioria dos quais têm interfaces SQL de streaming incorporados, e todos os quais se integram com a Azure Event Hubs hoje:

- [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)
- [Azure Synapse Analytics (via Event Hubs Capture)](../event-grid/event-grid-event-hubs-integration.md)
- [Azure Databricks](/azure/databricks/scenarios/databricks-stream-from-eventhubs)
- [Apache Samza](https://samza.apache.org/learn/documentation/latest/connectors/eventhubs)
- [Apache Storm](event-hubs-storm-getstarted-receive.md)
- [Apache Spark](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink](event-hubs-kafka-flink-tutorial.md)
- [Riachos Akka](event-hubs-kafka-akka-streams-tutorial.md)

Os serviços e quadros listados podem geralmente adquirir fluxos de eventos e dados de referência diretamente de um conjunto diversificado de fontes através de adaptadores. A Kafka Streams só pode adquirir dados da Apache Kafka e os seus projetos de análise estão, portanto, bloqueados em Apache Kafka. Para utilizar dados de outras fontes, é-lhe exigido que primeiro importe dados em Apache Kafka com o quadro Kafka Connect.

Se tiver de utilizar a estrutura kafka streams em Azure, [a Apache Kafka em HDInsight](../hdinsight/kafka/apache-kafka-introduction.md) fornecer-lhe-á essa opção. Apache Kafka em HDInsight fornece controlo total sobre todos os aspetos de configuração de Apache Kafka, ao mesmo tempo que está totalmente integrado com vários aspetos da plataforma Azure, desde a colocação de domínio de falha/atualização até ao isolamento da rede até à monitorização da integração. 

## <a name="next-steps"></a>Passos seguintes
Este artigo forneceu uma introdução aos Centros de Eventos para Kafka. Para saber mais, consulte [o guia de desenvolvimento apache Kafka para o Azure Event Hubs.](apache-kafka-developer-guide.md)