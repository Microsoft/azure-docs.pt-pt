---
title: Casos de utilização comuns e cenários para o Azure Cosmos DB
description: 'Saiba mais sobre a parte superior, cinco casos de utilização para o Azure Cosmos DB: utilizador gerado conteúdo, log de eventos, dados de catálogo, dados de preferências do usuário e Internet das coisas (IoT).'
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 28a4cc854842b66a9fb61134e3ca9ac9a5f38fed
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65965601"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Casos de utilização comuns do Azure Cosmos DB
Este artigo fornece uma visão geral dos vários casos de utilização comuns para o Azure Cosmos DB.  As recomendações neste artigo, servem como ponto de partida, como desenvolver a sua aplicação com Cosmos DB.   

Depois de ler este artigo, será capaz de responder às seguintes perguntas: 

* Quais são os casos de utilização comuns para o Azure Cosmos DB?
* Quais são as vantagens da utilização do Azure Cosmos DB para aplicações de varejo?
* Quais são as vantagens da utilização do Azure Cosmos DB como um arquivo de dados para sistemas de Internet das coisas (IoT)?
* Quais são as vantagens da utilização do Azure Cosmos DB para aplicações web e móveis?

## <a name="introduction"></a>Introdução
[O Azure Cosmos DB](../cosmos-db/introduction.md) é o serviço de base de dados distribuída globalmente da Microsoft. O serviço foi concebido para permitir que os clientes de forma elástica (e independente) dimensionar o débito e armazenamento em qualquer número de regiões geográficas. Azure Cosmos DB é o primeiro serviço de base de dados globalmente distribuída no mercado hoje mesmo para oferecer abrangente [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/cosmos-db/) que abrange o débito, latência, disponibilidade e consistência. 

O Azure Cosmos DB é um de vários modelo distribuída base de dados global que é utilizado numa ampla variedade de aplicativos e de casos de utilização. É uma boa opção para qualquer [sem servidor](https://azure.com/serverless) aplicativo que precisa de tempos de resposta de ordem de milissegundos baixa e tem de dimensionar rapidamente e globalmente. Ele oferece suporte a vários modelos de dados (chave-valor, documentos, gráficos e colunas) e aceder a várias APIs para dados incluindo [API do Azure Cosmos DB para o MongoDB](mongodb-introduction.md), [API de SQL](documentdb-introduction.md), [Gremlin API](graph-introduction.md), e [API de tabelas](table-introduction.md) nativamente e de forma extensível. 

Seguem-se alguns atributos do Azure Cosmos DB que o tornam adequado para aplicativos de alto desempenho com ambição global.

* O Azure Cosmos DB nativamente particiona os dados para alta disponibilidade e escalabilidade. O Azure Cosmos DB garante de 99,99% de disponibilidade, débito, baixa latência e consistência em todas as contas de região única e todas as contas de várias regiões com consistência flexível e 99,999% de disponibilidade em todas as contas de base de dados de várias regiões de leitura.
* Azure Cosmos DB tem armazenamento SSD com tempos de resposta de ordem de milissegundo de latência baixa.
* O suporte do Azure Cosmos DB para níveis de consistência como prescrição vinculada, sessão e eventual e consistente prefixo permite total flexibilidade e de baixa taxa de desempenho de custo. Nenhum serviço de base de dados oferece muito mais flexibilidade, como o Azure Cosmos DB na consistência níveis. 
* Azure Cosmos DB tem um modelo de preços do compatível com dados flexível que medidores de armazenamento e débito de forma independente.
* Modelo de débito reservada do Azure Cosmos DB permite-lhe a pensar em termos de número de leituras/gravações em vez de CPU/memória/IOPs do hardware subjacente.
* Design permite-do Azure Cosmos DB que lhe aumentar a volumes enormes de pedido por ordem de biliões de pedidos por dia.

Esses atributos são úteis na web, móvel, jogos e aplicações IoT que precisam de tempos de resposta curtos e tem de lidar com quantidades gigantescas de leituras e escritas.

## <a name="iot-and-telematics"></a>IoT e telemática
Casos de utilização de IoT comumente partilham alguns padrões em como eles ingestão, processo e armazenarem dados.  Em primeiro lugar, esses sistemas precisam absorver rajadas de dados de sensores de dispositivo de várias localidades. Em seguida, estes sistemas de processam e analisar dados de transmissão em fluxo para derivar informações em tempo real. Os dados, em seguida, são arquivados para o armazenamento de frio para análise de lotes. O Microsoft Azure oferece incluindo o Azure Cosmos DB, Hubs de eventos do Azure, Azure Stream Analytics, Hub de notificação do Azure, Azure Machine Learning, Azure HDInsight e o Power BI de casos de utilização de serviços avançados que podem ser aplicados para IoT. 

![Arquitetura de referência do Azure Cosmos DB IoT](./media/use-cases/iot.png)

Aumentos repentinos de dados podem ser ingeridos pelo Event Hubs do Azure, que oferece ingestão de dados de débito elevado com baixa latência. Podem ser funneled dados ingeridos e que precisam de ser processados para informações em tempo real para o Azure Stream Analytics para análise em tempo real. Dados podem ser carregados para o Azure Cosmos DB para consultas ad hoc. Depois dos dados são carregados para o Azure Cosmos DB, os dados estão prontos para ser consultado. Além disso, os novos dados e as alterações aos dados existentes podem ser lidos no feed de alterações. Feed de alterações é um persistente, apenas os registos que armazena as alterações para contentores do Cosmos DB em ordem sequencial de acréscimo. A todos os dados ou apenas as alterações dos dados no Azure Cosmos DB podem ser utilizadas como dados de referência como parte da análise em tempo real. Além disso, dados ainda podem ser refinados e processados através da ligação de dados do Azure Cosmos DB para o HDInsight para tarefas do Pig, Hive ou Map/Reduce.  Dos dados refinados, em seguida, são carregados para o Azure Cosmos DB para o relatório.   

Para uma solução de IoT de exemplo através do Azure Cosmos DB, EventHubs e Storm, consulte a [repositório de exemplos do storm do hdinsight no GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Para obter mais informações sobre as ofertas do Azure para IoT, veja [criar a sua Internet das coisas](https://www.microsoft.com/en-us/internet-of-things). 

## <a name="retail-and-marketing"></a>Revenda e marketing
O Azure Cosmos DB é muito usado nas plataformas de comércio eletrônico da Microsoft, que executam o Windows Store e XBox Live. Também é utilizado no setor de varejo para armazenar dados de catálogo e para a origem em pipelines de processamento de pedidos do evento.

Cenários de utilização do catálogo de dados envolvem armazenam e consultam um conjunto de atributos para entidades como pessoas, lugares e produtos. Alguns exemplos de dados de catálogo são contas de utilizador, catálogos de produtos, registos de dispositivos IoT e fatura dos sistemas de materiais. Atributos para estes dados podem variar e podem mudar ao longo do tempo para satisfazer as necessidades de aplicação.

Considere um exemplo de um catálogo de produtos para um fornecedor de peças automóvel. Todas as partes podem ter seus próprios atributos além dos atributos comuns que partilham todas as partes. Além disso, os atributos para uma parte específica podem alterar o ano seguinte, quando é lançado um novo modelo. Azure Cosmos DB suporta esquemas flexíveis e dados hierárquicos e, portanto, é bem adequado para armazenar dados de catálogo de produtos.

![Azure arquitetura de referência do catálogo de varejo do Cosmos DB](./media/use-cases/product-catalog.png)

O Azure Cosmos DB, muitas vezes, é utilizado para a origem do evento para arquiteturas de condicionada por eventos de energia com o respetivo [feed de alterações](change-feed.md) funcionalidade. O feed de alterações fornece a capacidade de forma fiável e de forma incremental ler inserções e atualizações (por exemplo, eventos de ordem) feitas ao Azure Cosmos DB de microsserviços downstream. Esta funcionalidade pode ser aproveitada para fornecer um armazenamento persistente de evento como um mediador de mensagem para eventos de alteração de estado e fluxo de trabalho do unidade ordem processamento entre muitos microsserviços (que pode ser implementado como [as funções do Azure sem servidor](https://azure.com/serverless)).

![O Azure Cosmos DB, a ordenação de arquitetura de referência do pipeline](./media/use-cases/event-sourcing.png)

Além disso, os dados armazenados no Azure Cosmos DB podem ser integrados com o HDInsight para análise de macrodados através de tarefas do Apache Spark. Para obter detalhes sobre o conector do Spark para o Azure Cosmos DB, consulte [executar uma tarefa do Spark com o Cosmos DB e o HDInsight](spark-connector.md).

## <a name="gaming"></a>Jogos
O escalão de base de dados é um componente essencial das aplicações de jogos. Os jogos modernos executam processamento gráfico em clientes móveis/consola, mas confiam na cloud para fornecer conteúdo personalizado e personalizado, como estatísticas nos jogos, integração com redes sociais e classificações de pontuações. Jogos requerem frequentemente latências de milissegundos únicas para leituras e escritas para fornecer um envolvimento no jogo experiência. Uma base de dados de jogo precisa ser rápido e conseguir gerir picos massivos nas velocidades de pedidos durante novos lançamentos de jogos e atualizações de funcionalidades.

O Azure Cosmos DB é utilizado pelos jogos como [The Walking Dead: Man s Land](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) pela [Next Games](https://www.nextgames.com/), e [Halo 5: Guardiões](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). O Azure Cosmos DB fornece as seguintes vantagens para os desenvolvedores de jogos:

* O Azure Cosmos DB permite que o desempenho de ser dimensionada ou reduz verticalmente de forma elástica. Isso permite que os jogos lidar com o perfil de atualização e estatísticas de dezenas de milhões de jogadores simultâneos, fazendo uma única chamada à API.
* O Azure Cosmos DB suporta milissegundo leituras e escritas para o ajudar a evitar qualquer lags durante o jogo.
* Indexação automática do Azure Cosmos DB permite a filtragem em relação a várias propriedades diferentes em tempo real, por exemplo, localizar jogadores por seus IDs de player interno ou seus GameCenter, Facebook, Google IDs ou consulta com base na associação player um guild. Isso é possível sem a criação de indexação complexas ou a infraestrutura de fragmentação.
* Funcionalidades de redes sociais, incluindo mensagens de chat no jogo, as associações de guild player, os desafios concluídos, classificações de pontuações e gráficos de redes sociais são mais fáceis de implementar com um esquema flexível.
* O Azure Cosmos DB como uma plataforma-como-um-serviço gerida (PaaS) necessária configuração mínima e gestão de trabalho para permitir uma rápida iteração e reduzem o tempo de comercialização.

![Arquitetura de referência de jogos do Azure Cosmos DB](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Aplicações móveis e Web
O Azure Cosmos DB é frequentemente utilizado em aplicações web e móveis e é ideal para modelar as interações de redes sociais, integração com os serviços de terceiros e para a criação de experiências personalizadas e sofisticadas. Os SDKs do Cosmos DB pode ser utilizado de compilação avançado aplicações iOS e Android com a popular [Xamarin framework](mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Aplicações de redes sociais
É um caso de utilização comuns para o Azure Cosmos DB para armazenar e consultar conteúdos gerados pelo utilizador (UGC) para a web, aplicativos de mídia social e móveis. Alguns exemplos de UGC são sessões de bate-papo, tweets, postagens em blogs, classificações e comentários. Muitas vezes, UGC em aplicativos de mídia social é uma mistura de texto de forma livre, propriedades, etiquetas e as relações que não estão limitadas pela estrutura rígida. Conteúdo, como bate-papos, comentários e mensagens podem ser armazenados no Cosmos DB sem a necessidade de transformações ou o objeto complexo para as camadas de mapeamento relacional.  Propriedades de dados podem ser adicionadas ou modificadas facilmente para corresponder aos requisitos, como os programadores iterar sobre o código do aplicativo, assim, promover o desenvolvimento rápido.  

Aplicativos que se integram com redes sociais de terceiros tem de responder às mudanças esquemas a partir destas redes. Como os dados forem indexados automaticamente por predefinição no Cosmos DB, dados estão prontos para ser consultados em qualquer altura. Por conseguinte, estas aplicações têm a flexibilidade para recuperar as projeções de acordo com suas respectivas necessidades.

Muitos dos aplicativos sociais executam à escala global e podem usar padrões de utilização imprevisíveis. Flexibilidade para dimensionar o armazenamento de dados é essencial que a camada de aplicativo pode ser dimensionada para corresponder à demanda de uso.  Pode aumentar horizontalmente ao adicionar partições de dados adicionais com uma conta do Cosmos DB.  Além disso, também pode criar contas adicionais do Cosmos DB em várias regiões. Para a disponibilidade de região do serviço de Cosmos DB, consulte [regiões do Azure](https://azure.microsoft.com/regions/#services).

![Arquitetura do referência de aplicações de web do Azure Cosmos DB](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>Personalização
Hoje em dia, os aplicativos modernos são fornecidos com modos de exibição complexos e experiências. Estes são normalmente dinâmicos, fornecendo as preferências do usuário ou reagem e necessidades de imagem corporativa. Por conseguinte, os aplicativos precisam de ser possível obter as definições personalizadas com eficiência para compor rapidamente experiências e elementos de interface do Usuário. 

JSON, formato suportado pela Cosmos DB, é um formato eficaz para representar os dados de layout de interface do Usuário que não só é simples, mas também pode ser facilmente interpretado pelo JavaScript. O cosmos DB oferece os níveis de consistência sincronizáveis que permitem que as leituras rápidas com gravações de baixa latência. Portanto, o armazenamento de dados de layout de interface do Usuário incluindo configurações personalizadas, como documentos JSON no Cosmos DB é um meio eficaz para obter esses dados pela conexão.

![Arquitetura do referência de aplicações de web do Azure Cosmos DB](./media/use-cases/personalization.png)

## <a name="next-steps"></a>Passos Seguintes
Para começar a utilizar com o Azure Cosmos DB, siga nosso [inícios Rápidos](create-sql-api-dotnet.md), que explica como criar uma conta e começar a trabalhar com o Cosmos DB. 

Ou, se quiser ler mais sobre os clientes com o Cosmos DB, estão disponíveis as seguintes histórias de clientes:

* [Jet.com](https://jet.com). Postos na liderança de comércio eletrônico olhos, é executado em nuvem da Microsoft, tira partido do Cosmos DB à escala global.
* [Asos.com](https://www.asos.com/). Asos.com é um arquivo British online de forma e a beleza. Principalmente com o objetivo de adultos jovens, Asos vende mais 850 marcas, bem como seu próprio intervalo de vestuário e acessórios.
* [Toyota](https://www.toyota.com/). Corporation Motor da Toyota é um fabricante automóvel japonês. Toyota tirar partido do Cosmos DB para aplicações de IoT global.
* [Citrix](https://customers.microsoft.com/story/citrix). Citrix desenvolve a solução de início de início de sessão único com o Azure Service Fabric e o Azure Cosmos DB
* [TEXA](https://customers.microsoft.com/story/texaspa) solução IoT revolucionária do TEXA para proprietários de veículos ajuda a economizar tempo, dinheiro, gás — e, possivelmente, reside.
* [Pizza do Domino](https://www.dominos.com). Inc. de Pizza do Domino é uma cadeia de restauração de American pizza.
* [Controla Johnson](https://www.johnsoncontrols.com). Controles Johnson é uma tecnologia diversificada global e com vários industrial líder que serve uma vasta gama de clientes em mais de 150 países/regiões.
* [Microsoft Windows Store Universal, IoT Hub do Azure, Xbox Live e outros serviços de escala da Internet](https://azure.microsoft.com/blog/how-azure-documentdb-planet-scale-nosql-helps-run-microsoft-s-own-businesses/). Como Microsoft baseia-se a serviços escaláveis em massa com o Azure Cosmos DB.
* [Equipe Microsoft Data e Analytics](https://customers.microsoft.com/story/microsoftdataandanalytics). Da Microsoft, equipe de dados e análise alcança a recolha de macrodados de escala planetária com o Azure Cosmos DB
* [Sulekha.com](https://customers.microsoft.com/story/sulekha-uses-azure-documentdb-to-connect-customers-and-businesses-across-india). Sulekha utiliza o Azure Cosmos DB para ligar os clientes e as empresas na Índia.
* [NewOrbit](https://customers.microsoft.com/story/neworbit-takes-flight-with-azure-documentdb). NewOrbit demora voo com o Azure Cosmos DB.
* [A Affinio](https://customers.microsoft.com/doclink/affinio-switches-from-aws-to-azure-documentdb-to-harness-social-data-at-scale). A Affinio muda do AWS para o Azure Cosmos DB para dados de redes sociais em escala.
* [Next Games](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/). A movimentação inutilizado: Man s Land jogos voa para #1 suportado pelo Azure Cosmos DB.
* [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Como o Halo 5 implementou a jogabilidade social com o Azure Cosmos DB.
* [Galeria do Cortana Analytics](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/). Cortana Analytics Galeria - um site de Comunidade escalável, criado no Azure Cosmos DB.
* [Breeze](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). Levando o integrador de dá informações Global de empresas multinacionais em minutos com as tecnologias de nuvem flexível.
* [A News Republic](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Adicionar inteligência às notícias para fornecer informações com o objetivo de cidadãos envolvidos. 
* [SGS International tem agora](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Para obter cores consistentes em todo o mundo, marcas principais ativar ao SGS. E SGS recorre ao Azure.
* [A Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Líder global Telenor utiliza a cloud para movimentar com agilidade Startup. 
* [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). O arquivo do futuro é executado em pesquisa rápida e fácil fluxo de dados.
* [A Nucleo](https://customers.microsoft.com/story/azure-based-software-platform-breaks-down-barriers-bet). Plataforma de software baseado no Azure divide as barreiras entre as empresas e clientes
* [Weka](https://customers.microsoft.com/story/weka-smart-fridge-improves-vaccine-management-so-more-people-can-be-protected-against-diseases). Frigorífico inteligente da weka melhora a gestão de vacinas, de modo mais pessoas podem ser protegidas contra doenças
* [Cor de laranja Tribes](https://customers.microsoft.com/story/theres-more-to-that-food-app-than-meets-the-eye-or-the-mouth). Há mais para essa aplicação comida que cumpre os olhos ou da boca.
* [O real Madrid](https://customers.microsoft.com/story/real-madrid-brings-the-stadium-closer-to-450-million-f). Real Madrid reúne o estádio a 450 milhões de fãs em todo o mundo, com a Microsoft Cloud.
* [Tuku](https://customers.microsoft.com/story/tuku-makes-car-buying-fun-with-help-from-azure-services). TUKU torna o carro comprando se divertir com a ajuda dos serviços do Azure
