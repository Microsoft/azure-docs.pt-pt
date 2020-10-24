---
title: Casos e cenários de uso comum para Azure Cosmos DB
description: 'Conheça os cinco principais casos de utilização para Azure Cosmos DB: conteúdo gerado pelo utilizador, registo de eventos, dados de catálogo, dados de preferências de utilizador e Internet das Coisas (IoT).'
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: b8d74f9da62b0f5a596c811efa9f93b55c99f1d7
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92476353"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Casos de utilização comuns do Azure Cosmos DB

Este artigo fornece uma visão geral de vários casos de uso comum para Azure Cosmos DB.  As recomendações deste artigo servem como ponto de partida à medida que desenvolve a sua aplicação com a Cosmos DB.

Depois de ler este artigo, poderá responder às seguintes perguntas: 

* Quais são os casos de uso comum para Azure Cosmos DB?
* Quais são os benefícios da utilização da Azure Cosmos DB para aplicações de retalho?
* Quais são os benefícios de usar a Azure Cosmos DB como uma loja de dados para sistemas de Internet das Coisas (IoT) ?
* Quais são os benefícios de usar a Azure Cosmos DB para aplicações web e móveis?

## <a name="introduction"></a>Introdução

[Azure Cosmos DB](../cosmos-db/introduction.md) é a base de dados NoSQL rápida da Microsoft com APIs abertos para qualquer escala. O serviço destina-se a permitir aos clientes dimensionar elasticamente (e de forma independente) a produção e armazenamento em qualquer número de regiões geográficas. A Azure Cosmos DB é hoje o primeiro serviço de base de dados distribuído globalmente no mercado para oferecer [acordos abrangentes](https://azure.microsoft.com/support/legal/sla/cosmos-db/) de nível de serviço que englobam a produção, latência, disponibilidade e consistência.

Azure Cosmos DB é uma base de dados global distribuída e multi-modelo que é usada numa ampla gama de aplicações e casos de utilização. É uma boa escolha para qualquer aplicação [sem servidor](https://azure.com/serverless) que precisa de tempos de resposta de baixa ordem de milissegundos, e precisa de escalar rapidamente e globalmente. Suporta vários modelos de dados (valor-chave, documentos, gráficos e colunares) e muitas APIs para acesso a dados, incluindo [a API da Azure Cosmos para MongoDB,](mongodb-introduction.md) [SQL API,](./introduction.md) [Gremlin API,](graph-introduction.md)e [Tabelas API](table-introduction.md) de forma nativa, e de forma extensível. 

Seguem-se alguns atributos da Azure Cosmos DB que o tornam bem adaptado para aplicações de alto desempenho com ambição global.

* A Azure Cosmos DB partilha os seus dados de forma nativa para uma elevada disponibilidade e escalabilidade. O Azure Cosmos DB oferece 99,99% de garantias de disponibilidade, produção, baixa latência e consistência em todas as contas de uma região única e todas as contas multi-regiões com consistência descontraída, e 99,999% ler disponibilidade em todas as contas de base de dados multi-regiões.
* A Azure Cosmos DB tem armazenamento apoiado em SSD com tempos de resposta de baixa latência de milissegundos.
* O suporte da Azure Cosmos DB para níveis de consistência como um prefixo, sessão e sessão eventual, consistente e limitado permite a flexibilidade total e a relação custo-desempenho baixa. Nenhum serviço de base de dados oferece tanta flexibilidade como o Azure Cosmos DB em níveis de consistência. 
* A Azure Cosmos DB tem um modelo de preços flexível e amigo dos dados que medi o armazenamento e a produção independente.
* O modelo de produção reservado da Azure Cosmos DB permite-lhe pensar em termos de número de leituras/escritas em vez de CPU/memória/IOPs do hardware subjacente.
* O design da Azure Cosmos DB permite escalar para volumes de pedidos maciços na ordem dos triliões de pedidos por dia.

Estes atributos são benéficos em aplicações web, mobile, gaming e IoT que precisam de tempos de resposta baixos e precisam lidar com quantidades massivas de leituras e escritos.

## <a name="iot-and-telematics"></a>IoT e telemática

Os casos de uso de IoT geralmente partilham alguns padrões na forma como ingerem, processam e armazenam dados.  Em primeiro lugar, estes sistemas precisam de ingerir explosões de dados a partir de sensores de dispositivos de vários locais. Em seguida, estes sistemas processam e analisam dados de streaming para obter insights em tempo real. Os dados são então arquivados para armazenamento frio para análise de lote. O Microsoft Azure oferece serviços ricos que podem ser aplicados para casos de uso IoT, incluindo Azure Cosmos DB, Azure Event Hubs, Azure Stream Analytics, Azure Notification Hub, Azure Machine Learning, Azure HDInsight e Power BI. 

:::image type="content" source="./media/use-cases/iot.png" alt-text="Arquitetura de referência de referência Azure Cosmos DB IoT" border="false":::

Explosões de dados podem ser ingeridas por Azure Event Hubs, uma vez que oferece alta ingestão de dados de produção com baixa latência. Os dados ingeridos que precisam de ser processados para uma visão em tempo real podem ser canalizados para a Azure Stream Analytics para análise em tempo real. Os dados podem ser carregados em Azure Cosmos DB para consulta de adhoc. Uma vez que os dados são carregados em Azure Cosmos DB, os dados estão prontos para serem consultados. Além disso, novos dados e alterações aos dados existentes podem ser lidos no feed de alteração. O feed de alteração é um registo persistente, apenas anexado que armazena alterações em recipientes Cosmos por ordem sequencial. Todos os dados ou apenas alterações aos dados em Azure Cosmos DB podem ser usados como dados de referência como parte da análise em tempo real. Além disso, os dados podem ser ainda refinados e processados ligando os dados DB do Azure Cosmos ao HDInsight for Pig, Hive ou Map/Reduce jobs.  Os dados refinados são então carregados de volta para Azure Cosmos DB para reporte.   

Para obter uma solução IoT de amostra usando Azure Cosmos DB, EventHubs e Storm, consulte o [repositório hdinsight-storm-exemplos no GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Para obter mais informações sobre as ofertas Azure para IoT, consulte [Create the Internet of Your Things](https://www.microsoft.com/en-us/internet-of-things). 

## <a name="retail-and-marketing"></a>Retalho e marketing
O Azure Cosmos DB é utilizado extensivamente nas plataformas de e-commerce da Microsoft, que gerem a Windows Store e o XBox Live. É também utilizado no sector retalhista para armazenar dados de catálogo e para fornecimento de eventos em pipelines de processamento de encomendas.

Os cenários de utilização de dados do catálogo envolvem armazenar e consultar um conjunto de atributos para entidades como pessoas, locais e produtos. Alguns exemplos de dados de catálogo são contas de utilizadores, catálogos de produtos, registos de dispositivos IoT e sistemas de fatura de materiais. Os atributos para estes dados podem variar e podem mudar ao longo do tempo para adequar os requisitos de aplicação.

Considere um exemplo de um catálogo de produtos para um fornecedor de peças para automóveis. Cada parte pode ter os seus próprios atributos, além dos atributos comuns que todas as partes partilham. Além disso, os atributos para uma peça específica podem mudar no ano seguinte quando um novo modelo é lançado. O Azure Cosmos DB suporta esquemas flexíveis e dados hierárquicos, pelo que é adequado para armazenar dados do catálogo de produtos.

:::image type="content" source="./media/use-cases/product-catalog.png" alt-text="Arquitetura de referência de referência Azure Cosmos DB IoT" border="false":::

O Azure Cosmos DB é frequentemente usado para o fornecimento de eventos para potenciar arquiteturas orientadas para eventos usando a sua funcionalidade [de feed de mudança.](change-feed.md) O feed de alteração fornece microserviços a jusante a capacidade de ler de forma fiável e incremental inserções e atualizações (por exemplo, eventos de encomenda) feitos a um Azure Cosmos DB. Esta funcionalidade pode ser alavancada para fornecer uma loja de eventos persistente como um corretor de mensagens para eventos que mudam o estado e impulsionar o fluxo de processamento de encomendas entre muitos microserviços (que podem ser implementados como [funções de Azure sem servidor).](https://azure.com/serverless)

:::image type="content" source="./media/use-cases/event-sourcing.png" alt-text="Arquitetura de referência de referência Azure Cosmos DB IoT" border="false":::

Além disso, os dados armazenados no Azure Cosmos DB podem ser integrados com HDInsight para análise de big data através de empregos Apache Spark. Para mais detalhes sobre o Conector de Faíscas para Azure Cosmos DB, consulte [Executar um trabalho de Spark com Cosmos DB e HDInsight](spark-connector.md).

## <a name="gaming"></a>Jogos
A camada da base de dados é um componente crucial das aplicações de jogos. Os jogos modernos executam o processamento gráfico em clientes móveis/de consola, mas contam com a cloud para disponibilizar conteúdo personalizado, como as estatísticas no jogo, a integração de redes sociais e as classificações com as pontuações mais altas. Os jogos requerem frequentemente latências de um milisegundo para leituras e escritas para proporcionar uma experiência envolvente no jogo. Uma base de dados de jogos precisa de ser rápida e conseguir lidar com grandes picos em taxas de pedidos durante os lançamentos de novos jogos e nas atualizações de funcionalidades.

Azure Cosmos DB é usado por jogos como [The Walking Dead: No Man's Land](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) by Next [Games](https://www.nextgames.com/)e [Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). A Azure Cosmos DB fornece os seguintes benefícios aos desenvolvedores de jogos:

* AZure Cosmos DB permite que o desempenho seja dimensionado para cima ou para baixo elasticamente. Isto permite que os jogos lidem com o perfil de atualização e estatísticas de dezenas a milhões de jogadores simultâneos, fazendo uma única chamada de API.
* Azure Cosmos DB suporta leituras e escritos milissegundos para ajudar a evitar quaisquer atrasos durante o jogo.
* A indexação automática da Azure Cosmos DB permite filtrar várias propriedades diferentes em tempo real, por exemplo, localizar os jogadores pelos seus IDs internos de jogadores, ou o seu GameCenter, Facebook, Google IDs ou consulta com base na adesão de jogadores a uma guilda. Isto é possível sem construir infraestruturas complexas de indexação ou de fragmentos.
* Funcionalidades sociais, incluindo mensagens de chat no jogo, membros do sindicato dos jogadores, desafios concluídos, leaderboards de pontuação alta e gráficos sociais são mais fáceis de implementar com um esquema flexível.
* A Azure Cosmos DB como uma plataforma gerida como um serviço (PaaS) exigiu um trabalho mínimo de configuração e gestão para permitir uma iteração rápida e reduzir o tempo ao mercado.

:::image type="content" source="./media/use-cases/gaming.png" alt-text="Arquitetura de referência de referência Azure Cosmos DB IoT" border="false":::

## <a name="web-and-mobile-applications"></a>Aplicações Web e móveis
O Azure Cosmos DB é frequentemente utilizado em aplicações Web e em aplicações móveis e é ideal para modelar interações sociais, integrar com serviços de terceiros e para a criação de experiências personalizadas. Os Cosmos DB SDKs podem ser usados para construir aplicações ricas em iOS e Android usando a popular [estrutura de Xamarin.](mobile-apps-with-xamarin.md)  

### <a name="social-applications"></a>Aplicações Sociais
Um caso de uso comum para Azure Cosmos DB é armazenar e consultar conteúdo gerado pelo utilizador (UGC) para aplicações web, móveis e redes sociais. Alguns exemplos da UGC são sessões de chat, tweets, posts de blog, audiências e comentários. Muitas vezes, a UGC nas aplicações das redes sociais é uma mistura de texto de forma livre, propriedades, etiquetas e relacionamentos que não são limitados por estrutura rígida. Conteúdos como chats, comentários e posts podem ser armazenados em Cosmos DB sem exigir transformações ou objeto complexo a camadas de mapeamento relacional.  As propriedades de dados podem ser adicionadas ou modificadas facilmente para corresponder aos requisitos como desenvolvedores iteram sobre o código de aplicação, promovendo assim o desenvolvimento rápido.  

As aplicações que se integram com redes sociais de terceiros devem responder à mudança de esquemas destas redes. Como os dados são automaticamente indexados por padrão na Cosmos DB, os dados estão prontos para serem consultados a qualquer momento. Por conseguinte, estas aplicações têm a flexibilidade para recuperar as projeções de acordo com as respetivas necessidades.

Muitas das aplicações sociais são executadas à escala global e podem apresentar padrões de utilização imprevisíveis. A flexibilidade na escala da loja de dados é essencial, uma vez que a camada de aplicação se escala para corresponder à procura de utilização.  Pode escalar adicionando divisórias de dados adicionais numa conta Cosmos DB.  Além disso, também pode criar contas adicionais de Cosmos DB em várias regiões. Para disponibilidade da região de serviços Cosmos DB, consulte [regiões de Azure.](https://azure.microsoft.com/regions/#services)

:::image type="content" source="./media/use-cases/apps-with-global-reach.png" alt-text="Arquitetura de referência de referência Azure Cosmos DB IoT" border="false":::

### <a name="personalization"></a>Personalização
Hoje em dia, as aplicações modernas vêm com vistas e experiências complexas. Estes são tipicamente dinâmicos, atendendo às preferências do utilizador ou humores e necessidades de marca. Assim, as aplicações precisam ser capazes de recuperar configurações personalizadas de forma eficaz para tornar os elementos e experiências de UI rapidamente. 

JSON, um formato suportado pela Cosmos DB, é um formato eficaz para representar dados de layout de UI, uma vez que não só é leve, como também pode ser facilmente interpretado pelo JavaScript. Cosmos DB oferece níveis de consistência incapazes que permitem leituras rápidas com baixas escritas de latência. Assim, armazenar dados de layout de UI, incluindo configurações personalizadas como documentos JSON em Cosmos DB é um meio eficaz para obter estes dados através do fio.

:::image type="content" source="./media/use-cases/personalization.png" alt-text="Arquitetura de referência de referência Azure Cosmos DB IoT" border="false":::

## <a name="next-steps"></a>Passos seguintes

* Para começar com a Azure Cosmos DB, siga os nossos [rápidos começos](create-sql-api-dotnet.md), que o acompanham através da criação de uma conta e começa com a Cosmos DB.

* Se quiser ler mais sobre clientes que usam Azure Cosmos DB, consulte a página de [estudos de caso do cliente.](https://azure.microsoft.com/case-studies/?service=cosmos-db)