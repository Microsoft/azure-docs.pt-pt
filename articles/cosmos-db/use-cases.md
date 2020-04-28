---
title: Casos e cenários de uso comum para Azure Cosmos DB
description: 'Conheça os cinco principais casos de utilização do Azure Cosmos DB: conteúdo gerado pelo utilizador, registo de eventos, dados de catálogo, dados de preferências dos utilizadores e Internet das Coisas (IoT).'
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: de2bc551547706fb820813e57996e77bf49148d1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73888941"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Casos de utilização de DB Da Azure Cosmos
Este artigo fornece uma visão geral de vários casos de uso comum para O Azure Cosmos DB.  As recomendações neste artigo servem como ponto de partida à medida que desenvolve a sua aplicação com cosmos DB.   

Depois de ler este artigo, poderá responder às seguintes perguntas: 

* Quais são os casos de uso comum para o Azure Cosmos DB?
* Quais são os benefícios da utilização do Azure Cosmos DB para aplicações de retalho?
* Quais são os benefícios da utilização do Azure Cosmos DB como uma loja de dados para sistemas de Internet das Coisas (IoT).
* Quais são os benefícios da utilização do Azure Cosmos DB para aplicações web e móveis?

## <a name="introduction"></a>Introdução
[O Azure Cosmos DB](../cosmos-db/introduction.md) é o serviço de base de dados distribuído globalmente pela Microsoft. O serviço destina-se a permitir aos clientes uma escala elástica (e de forma independente) de entrada e armazenamento em várias regiões geográficas. A Azure Cosmos DB é hoje o primeiro serviço de base de dados distribuído globalmente no mercado a oferecer [acordos abrangentes](https://azure.microsoft.com/support/legal/sla/cosmos-db/) de nível de serviço, abrangendo a produção, latência, disponibilidade e consistência. 

Azure Cosmos DB é uma base de dados global distribuída e multi-modelo que é usada numa ampla gama de aplicações e casos de uso. É uma boa escolha para qualquer aplicação [sem servidor](https://azure.com/serverless) que precise de baixos tempos de resposta de ordem de milissegundos, e precisa escalar rápida e globalmente. Suporta vários modelos de dados (valor-chave, documentos, gráficos e colunaar) e muitas APIs para acesso a dados, incluindo [a API do Azure Cosmos DB para MongoDB,](mongodb-introduction.md) [SQL API,](documentdb-introduction.md) [Gremlin API,](graph-introduction.md)e [Tabelas API](table-introduction.md) de forma nativa, e de forma extensível. 

Seguem-se alguns atributos do Azure Cosmos DB que o tornam adequado para aplicações de alto desempenho com ambição global.

* A Azure Cosmos DB partilha os seus dados de elevada disponibilidade e escalabilidade. A Azure Cosmos DB oferece 99,99% de garantias de disponibilidade, entrada, baixa latência e consistência em todas as contas unipessoais e em todas as contas multi-regiões com consistência descontraída, e 99,999% lê disponibilidade em todas as contas de bases de dados multi-regiões.
* A Azure Cosmos DB tem armazenamento apoiado por SSD com tempos de resposta de ordem de milissegundo sitia.
* O apoio da Azure Cosmos DB a níveis de consistência como eventual prefixo consistente, sessão e estagnação limitada permite a plena flexibilidade e relação custo-desempenho. Nenhum serviço de base de dados oferece tanta flexibilidade como o Azure Cosmos DB na consistência dos níveis. 
* A Azure Cosmos DB tem um modelo flexível de preços que mede o armazenamento e a produção de forma independente.
* O modelo de entrada reservado da Azure Cosmos DB permite-lhe pensar em termos de número de leituras/escritos em vez de CPU/memory/IOPs do hardware subjacente.
* O design da Azure Cosmos DB permite-lhe escalar para volumes de pedidos maciços na ordem dos triliões de pedidos por dia.

Estes atributos são benéficos em aplicações web, mobile, gaming e IoT que precisam de tempos de resposta baixos e precisam lidar com quantidades massivas de leituras e escritos.

## <a name="iot-and-telematics"></a>IoT e telemática
Os casos de uso de IoT geralmente partilham alguns padrões na forma como ingerir, processar e armazenar dados.  Em primeiro lugar, estes sistemas precisam de ingerir explosões de dados a partir de sensores de dispositivos de vários locais. Em seguida, estes sistemas processam e analisam dados de streaming para obter insights em tempo real. Os dados são então arquivados para armazenamento frio para análise de lotes. O Microsoft Azure oferece serviços ricos que podem ser aplicados para casos de uso ioT, incluindo Azure Cosmos DB, Azure Event Hubs, Azure Stream Analytics, Azure Notification Hub, Azure Machine Learning, Azure HDInsight e Power BI. 

![Arquitetura de referência Azure Cosmos DB IoT](./media/use-cases/iot.png)

Explosões de dados podem ser ingeridas por Azure Event Hubs, uma vez que oferece ingestão de dados de alta produção com baixa latência. Os dados ingeridos que precisam de ser processados para uma visão em tempo real podem ser canalizados para o Azure Stream Analytics para análise em tempo real. Os dados podem ser carregados no Azure Cosmos DB para consulta adhoc. Uma vez que os dados são carregados no Azure Cosmos DB, os dados estão prontos para serem consultados. Além disso, novos dados e alterações aos dados existentes podem ser lidos no feed de alterações. A mudança de alimentos é um registo persistente, apêndice que armazena alterações nos contentores Cosmos por ordem sequencial. Todos os dados ou apenas alterações aos dados em Azure Cosmos DB podem ser usados como dados de referência como parte da análise em tempo real. Além disso, os dados podem ser ainda refinados e processados ligando os dados do Azure Cosmos DB ao HDInsight para Pig, Hive ou Map/Reduce jobs.  Os dados refinados são então carregados de volta para o Azure Cosmos DB para reportagem.   

Para obter uma solução IoT de amostra utilizando O Azure Cosmos DB, EventHubs e Storm, consulte o [repositório hdinsight-storm-examples no GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Para obter mais informações sobre as ofertas do Azure para IoT, consulte [Create the Internet of Your Things](https://www.microsoft.com/en-us/internet-of-things). 

## <a name="retail-and-marketing"></a>Retalho e marketing
O Azure Cosmos DB é utilizado extensivamente nas próprias plataformas de e-commerce da Microsoft, que gerem a Windows Store e o XBox Live. É também utilizado na indústria retalhista para armazenar dados de catálogo e para abastecimento de eventos em pipelines de processamento de encomendas.

Cenários de utilização de dados de catálogo envolvem armazenar e consultar um conjunto de atributos para entidades como pessoas, lugares e produtos. Alguns exemplos de dados de catálogo são contas de utilizador, catálogos de produtos, registos de dispositivos IoT e sistemas de material. Os atributos para estes dados podem variar e podem mudar ao longo do tempo para ajustar os requisitos de aplicação.

Considere um exemplo de um catálogo de produtos para um fornecedor de peças para automóveis. Cada peça pode ter os seus próprios atributos, além dos atributos comuns que todas as partes partilham. Além disso, os atributos para uma parte específica podem mudar no ano seguinte quando um novo modelo é lançado. A Azure Cosmos DB suporta schemas flexíveis e dados hierárquicos, pelo que é adequado para armazenar dados do catálogo de produtos.

![Arquitetura de referência de catálogo de retalho Azure Cosmos DB](./media/use-cases/product-catalog.png)

O Azure Cosmos DB é frequentemente utilizado para o fornecimento de eventos para arquiteturas impulsionadas por eventos de energia usando a sua funcionalidade de [feed de mudança.](change-feed.md) O feed de mudança fornece microserviços a jusante a capacidade de ler de forma fiável e incremental inserções e atualizações (por exemplo, eventos de encomenda) feitos a um Azure Cosmos DB. Esta funcionalidade pode ser aproveitada para fornecer uma loja de eventos persistente como corretor de mensagens para eventos que mudam o estado e impulsionar o fluxo de trabalho de processamento de encomendas entre muitos microserviços (que podem ser implementados como [Funções Azure sem servidor).](https://azure.com/serverless)

![Azure Cosmos DB ordenando arquitetura de referência de pipeline](./media/use-cases/event-sourcing.png)

Além disso, os dados armazenados no Azure Cosmos DB podem ser integrados com o HDInsight para análise de big data através de empregos Apache Spark. Para mais detalhes sobre o Conector Spark para Azure Cosmos DB, consulte [Run a Spark job with Cosmos DB e HDInsight](spark-connector.md).

## <a name="gaming"></a>Jogos
O nível de base de dados é um componente crucial das aplicações de jogo. Os jogos modernos realizam processamento gráfico em clientes móveis/consolas, mas dependem da nuvem para fornecer conteúdo personalizado e personalizado, como estatísticas dentro do jogo, integração de redes sociais e quadros de alto rendimento. Os jogos requerem muitas vezes atrasos de um milissegundo para leituras e escritos para proporcionar uma experiência envolvente no jogo. Uma base de dados de jogos precisa de ser rápida e ser capaz de lidar com picos maciços nas taxas de pedido durante os lançamentos de novos jogos e atualizações de funcionalidades.

Azure Cosmos DB é usado por jogos como [The Walking Dead: No Man's Land](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) by Next [Games](https://www.nextgames.com/), e [Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). A Azure Cosmos DB oferece os seguintes benefícios aos desenvolvedores de jogos:

* O Azure Cosmos DB permite que o desempenho seja dimensionado para cima ou para baixo elástico. Isto permite que os jogos lidem com a atualização de perfis e estatísticas de dezenas a milhões de jogadores simultâneos, fazendo uma única chamada API.
* Azure Cosmos DB suporta leituras milissegundos e escreve para ajudar a evitar quaisquer atrasos durante o jogo.
* O indexante automático do Azure Cosmos DB permite filtrar contra várias propriedades diferentes em tempo real, por exemplo, localizar jogadores pelas suas IDs de jogadores internos, ou os seus GameCenter, Facebook, Google IDs ou consulta com base na adesão dos jogadores numa guilda. Isto é possível sem a construção de indexação complexa ou infraestrutura de sharding.
* As funcionalidades sociais, incluindo mensagens de chat no jogo, membros da associação de jogadores, desafios concluídos, tabelas de alto rendimento e gráficos sociais são mais fáceis de implementar com um esquema flexível.
* A Azure Cosmos DB como uma plataforma gerida como serviço (PaaS) exigiu um trabalho mínimo de configuração e gestão para permitir uma rápida iteração e reduzir o tempo para o mercado.

![Arquitetura de referência de jogos Azure Cosmos DB](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Aplicações web e móveis
O Azure Cosmos DB é comumente utilizado dentro de aplicações web e móveis, e é bem adequado para modelar interações sociais, integrando-se com serviços de terceiros, e para construir experiências personalizadas ricas. Os SDKs Cosmos DB podem ser usados para construir aplicações ricas em iOS e Android utilizando a popular [estrutura Xamarin.](mobile-apps-with-xamarin.md)  

### <a name="social-applications"></a>Aplicações Sociais
Um caso de uso comum para o Azure Cosmos DB é armazenar e consultar conteúdos gerados pelo utilizador (UGC) para aplicações web, móveis e redes sociais. Alguns exemplos da UGC são sessões de chat, tweets, posts de blog, classificações e comentários. Muitas vezes, a UGC em aplicações de redes sociais é uma mistura de texto de forma livre, propriedades, tags e relações que não são delimitadas por estrutura rígida. Conteúdos como conversas, comentários e posts podem ser armazenados em Cosmos DB sem exigir transformações ou objeto complexo a camadas de mapeamento relacional.  As propriedades de dados podem ser adicionadas ou modificadas facilmente para corresponder aos requisitos como iterato de desenvolvedores sobre o código de aplicação, promovendo assim o rápido desenvolvimento.  

As aplicações que se integram nas redes sociais de terceiros devem responder às mudanças de schemas destas redes. Como os dados são automaticamente indexados por padrão no Cosmos DB, os dados estão prontos para serem consultados a qualquer momento. Por conseguinte, estas aplicações têm a flexibilidade para recuperar projeções de acordo com as suas respetivas necessidades.

Muitas das aplicações sociais funcionam à escala global e podem apresentar padrões de utilização imprevisíveis. A flexibilidade na escalação da loja de dados é essencial, uma vez que a camada de aplicação se estoira para corresponder à procura de utilização.  Pode escalar adicionando divisórias adicionais de dados sob uma conta Cosmos DB.  Além disso, você também pode criar contas adicionais do Cosmos DB em várias regiões. Para a disponibilidade da região de serviço Cosmos DB, consulte [as Regiões Azure.](https://azure.microsoft.com/regions/#services)

![Arquitetura de referência da web app Azure Cosmos DB](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>Personalização
Hoje em dia, as aplicações modernas vêm com vistas e experiências complexas. Estes são tipicamente dinâmicos, atendendo às preferências do utilizador ou humores e necessidades de marca. Por isso, as aplicações precisam de ser capazes de recuperar configurações personalizadas de forma eficaz para tornar rapidamente elementos e experiências de UI. 

JSON, um formato suportado pela Cosmos DB, é um formato eficaz para representar dados de layout ui, uma vez que não é apenas leve, mas também pode ser facilmente interpretado pelo JavaScript. Cosmos DB oferece níveis de consistência insupreensíveis que permitem leituras rápidas com baixa latência escritas. Assim, armazenar dados de layout ui incluindo configurações personalizadas como documentos JSON em Cosmos DB é um meio eficaz para obter estes dados através do fio.

![Arquitetura de referência da web app Azure Cosmos DB](./media/use-cases/personalization.png)

## <a name="next-steps"></a>Passos seguintes

* Para começar com o Azure Cosmos DB, siga os nossos [rápidos começos](create-sql-api-dotnet.md), que o acompanham através da criação de uma conta e começando com cosmos DB.

* Se quiser ler mais sobre clientes que usam o Azure Cosmos DB, consulte a página de estudos de caso do [cliente.](https://azure.microsoft.com/case-studies/?service=cosmos-db)
