---
title: Casos de uso comuns e cenários para Azure Cosmos DB
description: 'Saiba mais sobre os cinco principais casos de uso para Azure Cosmos DB: conteúdo gerado pelo usuário, log de eventos, dados de catálogo, dados de preferências do usuário e Internet das Coisas (IoT).'
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: de2bc551547706fb820813e57996e77bf49148d1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888941"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Casos de uso comuns de Azure Cosmos DB
Este artigo fornece uma visão geral de vários casos de uso comuns para Azure Cosmos DB.  As recomendações neste artigo servem como ponto de partida à medida que você desenvolve seu aplicativo com Cosmos DB.   

Depois de ler este artigo, você poderá responder às seguintes perguntas: 

* Quais são os casos de uso comuns do Azure Cosmos DB?
* Quais são os benefícios de usar o Azure Cosmos DB para aplicativos de varejo?
* Quais são os benefícios de usar o Azure Cosmos DB como um armazenamento de dados para sistemas de Internet das Coisas (IoT)?
* Quais são os benefícios de usar o Azure Cosmos DB para aplicativos Web e móveis?

## <a name="introduction"></a>Introdução
[Azure Cosmos DB](../cosmos-db/introduction.md) é o serviço de banco de dados distribuído globalmente da Microsoft. O serviço foi projetado para permitir que os clientes dimensionem de forma elástica (e independente) a taxa de transferência e o armazenamento em qualquer número de regiões geográficas. Azure Cosmos DB é o primeiro serviço de banco de dados distribuído globalmente no mercado atualmente para oferecer [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/cosmos-db/) abrangentes que abrangem a taxa de transferência, latência, disponibilidade e consistência. 

O Azure Cosmos DB é um banco de dados distribuído global e multimodelo que é usado em uma ampla gama de aplicativos e casos de uso. É uma boa opção para qualquer aplicativo sem [servidor](https://azure.com/serverless) que precise de tempos de resposta baixos de ordem de milissegundo e que precise ser dimensionado de forma rápida e global. Ele dá suporte a vários modelos de dados (chave-valor, documentos, grafos e colunas) e muitas APIs para acesso a dados, incluindo [API de Azure Cosmos DB para MongoDB](mongodb-introduction.md), [API do SQL](documentdb-introduction.md), [API do Gremlin](graph-introduction.md)e API de [tabelas](table-introduction.md) nativamente e de maneira extensível. 

Veja a seguir alguns atributos de Azure Cosmos DB que o tornam adequado para aplicativos de alto desempenho com ambição global.

* Azure Cosmos DB particiona nativamente seus dados para alta disponibilidade e escalabilidade. A Azure Cosmos DB oferece 99,99% de garantia de disponibilidade, taxa de transferência, baixa latência e consistência em todas as contas de região única e todas as contas de várias regiões com consistência reduzida e 99,999% de disponibilidade de leitura em todas as contas de banco de dados de várias regiões.
* Azure Cosmos DB tem armazenamento com suporte de SSD com tempos de resposta de baixa latência de ordem de milissegundos.
* O suporte de Azure Cosmos DB para níveis de consistência como eventual, prefixo consistente, sessão e desatualização limitada permite total flexibilidade e taxa de baixo custo-desempenho. Nenhum serviço de banco de dados oferece tanta flexibilidade quanto Azure Cosmos DB em consistência de níveis. 
* O Azure Cosmos DB tem um modelo de preços amigável para dados flexível que monitora o armazenamento e a taxa de transferência de forma independente.
* O modelo de taxa de transferência reservada do Azure Cosmos DB permite que você considere em termos de número de leituras/gravações em vez de CPU/memória/IOPs do hardware subjacente.
* O design de Azure Cosmos DB permite que você dimensione para volumes de solicitação maciços na ordem de trilhões de solicitações por dia.

Esses atributos são benéficos em aplicativos Web, móveis, de jogos e IoT que precisam de tempos de resposta baixos e precisam lidar com grandes quantidades de leituras e gravações.

## <a name="iot-and-telematics"></a>IoT e telemática
Casos de uso de IoT normalmente compartilham alguns padrões em como eles ingerim, processam e armazenam dados.  Primeiro, esses sistemas precisam incluir intermitências de dados de sensores de dispositivo de várias localidades. Em seguida, esses sistemas processam e analisam dados de streaming para derivar informações em tempo real. Os dados são arquivados no armazenamento frio para análise em lote. O Microsoft Azure oferece serviços avançados que podem ser aplicados a casos de uso de IoT, incluindo Azure Cosmos DB, hubs de eventos do Azure, Azure Stream Analytics, Hub de notificação do Azure, Azure Machine Learning, Azure HDInsight e Power BI. 

![Arquitetura de referência de IoT Azure Cosmos DB](./media/use-cases/iot.png)

As intermitências de dados podem ser ingeridas pelos hubs de eventos do Azure, pois oferecem ingestão de dados de alta taxa de transferência com baixa latência. Os dados ingeridos que precisam ser processados para insights em tempo real podem ser encaminhados para Azure Stream Analytics para análise em tempo real. Os dados podem ser carregados em Azure Cosmos DB para consulta ad hoc. Depois que os dados são carregados no Azure Cosmos DB, os dados estão prontos para serem consultados. Além disso, novos dados e alterações nos dados existentes podem ser lidos no feed de alterações. O feed de alterações é um log persistente, acrescentar somente que armazena alterações em contêineres Cosmos em ordem sequencial. Todos os dados ou apenas alterações nos dados no Azure Cosmos DB podem ser usados como dados de referência como parte da análise em tempo real. Além disso, os dados podem ser refinados e processados, conectando Azure Cosmos DB dados ao HDInsight para Pig, Hive ou mapear/reduzir trabalhos.  Os dados refinados são então carregados de volta para Azure Cosmos DB para relatórios.   

Para obter uma solução de IoT de exemplo usando Azure Cosmos DB, EventHubs e Storm, consulte o [repositório hdinsight-Storm-examples no GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Para obter mais informações sobre as ofertas do Azure para IoT, consulte [criar a Internet das coisas](https://www.microsoft.com/en-us/internet-of-things). 

## <a name="retail-and-marketing"></a>Varejo e marketing
O Azure Cosmos DB é amplamente usado nas próprias plataformas de comércio eletrônico da Microsoft, que executam a Windows Store e o XBox Live. Ele também é usado no setor de varejo para armazenar dados de catálogo e para fornecimento de eventos em pipelines de processamento de pedidos.

Os cenários de uso de dados do catálogo envolvem armazenar e consultar um conjunto de atributos para entidades como pessoas, lugares e produtos. Alguns exemplos de dados de catálogo são contas de usuário, catálogos de produtos, registros de dispositivos IoT e sistemas de listas de materiais. Os atributos para esses dados podem variar e podem mudar ao longo do tempo para atender aos requisitos do aplicativo.

Considere um exemplo de um catálogo de produtos para um fornecedor de peças automotivas. Cada parte pode ter seus próprios atributos, além dos atributos comuns que todas as partes compartilham. Além disso, os atributos de uma parte específica podem alterar o ano seguinte quando um novo modelo é liberado. O Azure Cosmos DB dá suporte a esquemas flexíveis e dados hierárquicos e, portanto, é adequado para armazenar dados do catálogo de produtos.

![Arquitetura de referência do catálogo de varejo Azure Cosmos DB](./media/use-cases/product-catalog.png)

O Azure Cosmos DB geralmente é usado para fornecimento de evento para arquiteturas orientadas a eventos de energia usando sua funcionalidade de [feed de alterações](change-feed.md) . O feed de alterações fornece aos microserviços downstream a capacidade de ler de forma confiável e incremental as inserções e atualizações (por exemplo, eventos de pedido) feitas em um Azure Cosmos DB. Essa funcionalidade pode ser aproveitada para fornecer um repositório de eventos persistente como um agente de mensagens para eventos de alteração de estado e direcionar o fluxo de trabalho de processamento de pedidos entre muitos microserviços (que podem ser implementados como [Azure Functions sem servidor](https://azure.com/serverless)).

![Azure Cosmos DB a arquitetura de referência do pipeline de ordenação](./media/use-cases/event-sourcing.png)

Além disso, os dados armazenados no Azure Cosmos DB podem ser integrados ao HDInsight para Big Data Analytics por meio de trabalhos de Apache Spark. Para obter detalhes sobre o conector do Spark para Azure Cosmos DB, consulte [executar um trabalho do Spark com o cosmos DB e o HDInsight](spark-connector.md).

## <a name="gaming"></a>Jogos
A camada de banco de dados é um componente crucial dos aplicativos de jogos. Os jogos modernos executam o processamento gráfico em clientes móveis/de console, mas contam com a nuvem para fornecer conteúdo personalizado e personalizado, como estatísticas no jogo, integração de mídia social e placares de alta pontuação. Os jogos geralmente exigem latências de um milissegundo para leituras e gravações para fornecer uma experiência envolvente no jogo. Um banco de dados de jogos precisa ser rápido e ser capaz de lidar com grandes picos em taxas de solicitação durante novas atualizações de recursos e lançamentos de jogos.

A Azure Cosmos DB é usada por jogos como [o percorrendo: nenhum homem é o terreno dos](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) [próximos jogos](https://www.nextgames.com/)e o [Halo 5: tutores](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). O Azure Cosmos DB oferece os seguintes benefícios aos desenvolvedores de jogos:

* Azure Cosmos DB permite que o desempenho seja aumentado ou reduzido de forma elástica. Isso permite que os jogos manipulem o perfil de atualização e as estatísticas de dezenas a milhões de jogadores simultâneos fazendo uma única chamada à API.
* O Azure Cosmos DB dá suporte a leituras e gravações de milissegundos para ajudar a evitar qualquer atraso durante a reprodução do jogo.
* A indexação automática de Azure Cosmos DB permite a filtragem de várias propriedades diferentes em tempo real, por exemplo, localizar jogadores por suas IDs de Player internas ou suas GameCenter, Facebook, Google IDs ou consulta com base na associação do Player em um comunidade. Isso é possível sem criar uma infraestrutura complexa de indexação ou fragmentação.
* Recursos sociais, incluindo mensagens de bate-papo em jogo, associações do jogador Comunidade, desafios concluídos, placares de alta pontuação e grafos sociais são mais fáceis de implementar com um esquema flexível.
* Azure Cosmos DB como uma PaaS (plataforma como serviço) gerenciada exigia um trabalho mínimo de configuração e gerenciamento para permitir uma iteração rápida e reduzir o tempo de colocação no mercado.

![Arquitetura de referência de jogos Azure Cosmos DB](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Aplicações móveis e Web
O Azure Cosmos DB é comumente usado em aplicativos Web e móveis e é adequado para modelagem de interações sociais, integração com serviços de terceiros e para a criação de experiências personalizadas avançadas. Os SDKs de Cosmos DB podem ser usados para criar aplicativos iOS e Android avançados usando a [estrutura do Xamarin](mobile-apps-with-xamarin.md)popular.  

### <a name="social-applications"></a>Aplicativos sociais
Um caso de uso comum para o Azure Cosmos DB é armazenar e consultar o UGC (conteúdo gerado pelo usuário) para aplicativos Web, móveis e de mídia social. Alguns exemplos de UGC são sessões de bate-papo, tweets, Postagens de blog, classificações e comentários. Muitas vezes, o UGC em aplicativos de mídia social é uma mistura de texto livre, propriedades, marcas e relações que não são limitados pela estrutura rígida. Conteúdo, como bate-papos, comentários e mensagens podem ser armazenados no Cosmos DB sem a necessidade de transformações ou o objeto complexo para as camadas de mapeamento relacional.  As propriedades de dados podem ser adicionadas ou modificadas facilmente para atender aos requisitos conforme os desenvolvedores iteram sobre o código do aplicativo, promovendo assim o desenvolvimento rápido.  

Os aplicativos que se integram a redes sociais de terceiros devem responder à alteração de esquemas dessas redes. Como os dados são indexados automaticamente por padrão no Cosmos DB, os dados estão prontos para serem consultados a qualquer momento. Portanto, esses aplicativos têm a flexibilidade de recuperar projeções de acordo com suas respectivas necessidades.

Muitos dos aplicativos sociais são executados em escala global e podem apresentar padrões de uso imprevisíveis. A flexibilidade no dimensionamento do armazenamento de dados é essencial à medida que a camada do aplicativo é dimensionada para corresponder à demanda de uso.  Você pode escalar horizontalmente adicionando partições de dados adicionais em uma conta de Cosmos DB.  Além disso, você também pode criar contas de Cosmos DB adicionais em várias regiões. Para obter Cosmos DB disponibilidade da região de serviço, consulte [regiões do Azure](https://azure.microsoft.com/regions/#services).

![Arquitetura de referência do aplicativo Web Azure Cosmos DB](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>Personalização
Hoje em dia, os aplicativos modernos vêm com exibições e experiências complexas. Normalmente, eles são dinâmicos, atendendo às preferências do usuário ou a humor e às necessidades de identidade visual. Portanto, os aplicativos precisam ser capazes de recuperar configurações personalizadas com eficiência para renderizar rapidamente os elementos e as experiências da interface do usuário. 

O JSON, um formato com suporte pelo Cosmos DB, é um formato eficaz para representar dados de layout da interface do usuário, pois não é apenas leve, mas também pode ser facilmente interpretado por JavaScript. O Cosmos DB oferece níveis de consistência ajustáveis que permitem leituras rápidas com gravações de baixa latência. Portanto, armazenar dados de layout da interface do usuário, incluindo configurações personalizadas, como documentos JSON no Cosmos DB é um meio eficaz de obter esses dados em toda a conexão.

![Arquitetura de referência do aplicativo Web Azure Cosmos DB](./media/use-cases/personalization.png)

## <a name="next-steps"></a>Passos seguintes

* Para começar a usar o Azure Cosmos DB, siga nossos [inícios rápidos](create-sql-api-dotnet.md), que orientam você na criação de uma conta e na introdução ao cosmos DB.

* Se você quiser ler mais sobre os clientes usando Azure Cosmos DB, consulte a página [estudos de caso de cliente](https://azure.microsoft.com/case-studies/?service=cosmos-db) .
