---
title: Introdução ao Azure Cosmos DB
description: Saiba mais sobre o Azure Cosmos DB. Esta base de dados de distribuição global com vários modelos foi concebida para baixa latência, escalabilidade elástica e elevada disponibilidade, e fornece suporte nativo para dados NoSQL.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: bfac783fcb8ce88977fc8d97f821a7350f57aab8
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2020
ms.locfileid: "85118581"
---
# <a name="welcome-to-azure-cosmos-db"></a>Bem-vindo ao Azure Cosmos DB

As aplicações de hoje são necessárias para serem altamente responsivas e sempre online. Para alcançar baixa latência e elevada disponibilidade, é necessário implementar casos destas aplicações em centros de dados próximos dos seus utilizadores. As aplicações precisam de responder em tempo real a grandes mudanças no uso em horas de ponta, armazenar cada vez mais volumes de dados e disponibilizar estes dados aos utilizadores em milissegundos.

Azure Cosmos DB é o serviço de base de dados multi-modelo da Microsoft distribuído globalmente. Com um clique de um botão, o Cosmos DB permite-lhe escalar elasticamente e independentemente a produção e armazenamento em qualquer número de regiões de Azure em todo o mundo. Você pode dimensionar elasticamente a produção e armazenamento, e aproveitar o acesso rápido e de um dígito de dados usando a sua API favorita, incluindo: SQL, MongoDB, Cassandra, Tables ou Gremlin. A Cosmos DB oferece [acordos abrangentes de nível de serviço](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) (SLAs) para produção, latência, disponibilidade e garantias de consistência, algo que nenhum outro serviço de base de dados oferece.

Você pode [experimentar Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição Azure, gratuitamente e compromissos ou usar o [nível gratuito Azure Cosmos DB](optimize-dev-test.md#azure-cosmos-db-free-tier) para obter uma conta com os primeiros 400 RU/s e 5 GB de armazenamento gratuito.

> [!div class="nextstepaction"]
> [Experimente Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/)

:::image type="content" source="./media/introduction/azure-cosmos-db.png" alt-text="O Azure Cosmos DB é um serviço de base de dados de distribuição global da Microsoft com aumento horizontal elástico, baixa latência garantida, cinco modelos de consistência e SLAs abrangentes garantidos" border="false":::

## <a name="key-benefits"></a>Principais vantagens

### <a name="turnkey-global-distribution"></a>Distribuição global chave na mão

O Cosmos DB permite-lhe construir aplicações altamente responsivas e altamente disponíveis em todo o mundo. Cosmos DB replica transparentemente os seus dados onde quer que os seus utilizadores estejam, para que os seus utilizadores possam interagir com uma réplica dos dados que lhes são mais próximos.

Cosmos DB permite-lhe adicionar ou remover qualquer uma das regiões Azure na sua conta Cosmos a qualquer momento, com um clique de um botão. A Cosmos DB irá replicar os seus dados de forma perfeita para todas as regiões associadas à sua conta Cosmos, enquanto a sua aplicação continua altamente disponível, graças às capacidades *multi-homing* do serviço. Para mais informações, consulte o artigo [de distribuição global.](distribute-data-globally.md)

### <a name="always-on"></a>Sempre ligado

Em virtude da profunda integração com a infraestrutura Azure e [da replicação multi-master transparente,](global-dist-under-the-hood.md)a Cosmos DB proporciona [uma disponibilidade de 99,999% para](high-availability.md) leituras e escritas. Cosmos DB também lhe fornece a capacidade de invocar programáticamente (ou via Portal) a falha regional da sua conta Cosmos. Esta capacidade ajuda a garantir que a sua aplicação foi concebida para falhar em caso de desastre regional.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>Escalabilidade elástica de produção e armazenamento, em todo o mundo

Projetado com divisórias horizontais transparentes e replicação multi-master, cosmos DB oferece uma escalabilidade elástica sem precedentes para os seus escritos e leituras, em todo o mundo. Você pode dimensionar elasticamente de milhares a centenas de milhões de pedidos/seg em todo o mundo, com uma única chamada API e pagar apenas pela produção (e armazenamento) que você precisa. Esta capacidade ajuda-o a lidar com picos inesperados nas suas cargas de trabalho sem ter de exagerar no pico. Para obter mais informações, consulte [a partição em Cosmos DB,](partitioning-overview.md) [produção abastada em contentores e bases de dados,](set-throughput.md)e [dimensionamento provisitado globalmente.](scaling-throughput.md)

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>Latência baixa garantida no percentil 99, em todo o mundo

Usando o Cosmos DB, você pode construir aplicações altamente responsivas à escala de planetas. Com o seu novo protocolo de replicação multi-mestre e motor de base de dados sem fecho e [com a assinatura otimizada,](index-policy.md)a Cosmos DB garante menos de 10 atrasos para ambos, lê (indexado) e escreve no percentil 99, em todo o mundo. Esta capacidade permite a ingestão sustentada de dados e consultas rápidas para aplicações altamente responsivas.

### <a name="precisely-defined-multiple-consistency-choices"></a>Escolhas de consistência precisamente definidas e múltiplas

Ao construir aplicações distribuídas globalmente em Cosmos DB, já não é preciso fazer [trocas extremas entre consistência, disponibilidade, latência e produção.](consistency-levels-tradeoffs.md) O protocolo de replicação multi-master da Cosmos DB é cuidadosamente concebido para oferecer [cinco escolhas de consistência bem definidas,](consistency-levels.md)  -  *forte,* *delimitada,* *sessão,* *prefixo consistente*e *eventual* , para um modelo de programação intuitivo com baixa latência e alta disponibilidade para a sua aplicação distribuída globalmente.

### <a name="no-schema-or-index-management"></a>Sem schema ou gestão de índices

Manter o esquema de base de dados e os índices em sincronização com o esquema de uma aplicação é especialmente doloroso para aplicações distribuídas globalmente. Com a Cosmos DB, não precisa de lidar com schema ou gestão de índices. O motor da base de dados é totalmente agnóstico.  Uma vez que não é necessário um esquema e uma gestão de índices, também não tem de se preocupar com o tempo de inatividade da aplicação enquanto migra os esquemas. Cosmos DB [indexa automaticamente todos os dados](index-policy.md) e serve consultas rapidamente.

### <a name="battle-tested-database-service"></a>Serviço de base de dados testado em batalha

Cosmos DB é um serviço fundamental em Azure. Durante quase uma década, a Cosmos DB tem sido usada por muitos dos produtos da Microsoft para aplicações críticas de missão à escala global, incluindo Skype, Xbox, Office 365, Azure, entre muitos outros. Hoje, a Cosmos DB é um dos serviços que mais cresce no Azure, utilizado por muitos clientes externos e aplicações críticas de missão que requerem escala elástica, distribuição global chave na mão, replicação multi-master para baixa latência e alta disponibilidade de ambos os leituras e escritos.

### <a name="ubiquitous-regional-presence"></a>Presença regional ubíqua

Cosmos DB está disponível em todas as regiões do Azure em todo o mundo, incluindo 54 regiões em nuvem pública, [Azure China 21Vianet](https://www.azure.cn/en-us/), Azure Germany, Azure Government, e Azure Government for Department of Defense (DoD). Veja a [presença regional da Cosmos DB.](regional-presence.md)

### <a name="secure-by-default-and-enterprise-ready"></a>Seguro por defeito e empresa pronta

Cosmos DB é certificado para uma [grande variedade de padrões de conformidade](compliance.md). Além disso, todos os dados em Cosmos DB são encriptados em repouso e em movimento. Cosmos DB fornece autorização de nível de linha e adere a rigorosos padrões de segurança.

### <a name="significant-tco-savings"></a>Economia significativa de TCO

Uma vez que o Cosmos DB é um serviço totalmente gerido, já não precisa de gerir e operar implementações complexas de vários centros de dados e atualizações do seu software de base de dados, pagar pelo suporte, licenciamento ou operações ou ter de providenciar a sua base de dados para o pico da carga de trabalho. Para mais informações, consulte [o custo da Otimize com a Cosmos DB](total-cost-ownership.md).

### <a name="industry-leading-comprehensive-slas"></a>Indústria líder de SLAs abrangentes

Cosmos DB é o primeiro e único serviço a oferecer [SLAs abrangentes líderes do setor,](https://azure.microsoft.com/support/legal/sla/cosmos-db/) englobando 99,999% de alta disponibilidade, ler e escrever latência no percentil 99, produção garantida e consistência.

### <a name="globally-distributed-operational-analytics-and-ai-with-natively-built-in-apache-spark"></a>Análise operacional distribuída globalmente e IA com Apache Spark incorporado nativo

Pode executar [a Spark](spark-connector.md) diretamente nos dados armazenados na Cosmos DB. Esta capacidade permite-lhe fazer análises operacionais de baixa latência à escala global sem afetar cargas de trabalho transacionais que operam diretamente contra a Cosmos DB. Para obter mais informações, consulte [a análise operacional distribuída globalmente.](lambda-architecture.md)

### <a name="develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis"></a>Desenvolver aplicações em Cosmos DB usando APIs populares de Software Open Source (OSS)

Cosmos DB oferece uma escolha de APIs para trabalhar com os seus dados armazenados na sua base de dados Cosmos. Por padrão, [pode utilizar o SQL](how-to-sql-query.md) (uma API central) para consultar a sua base de dados Cosmos. Cosmos DB também implementa APIs para [Cassandra,](cassandra-introduction.md) [MongoDB,](mongodb-introduction.md) [Gremlin](graph-introduction.md) e [Azure Table Storage](table-introduction.md). Pode apontar os controladores clientes (e ferramentas) para o NoSQL comumente utilizado (por exemplo, MongoDB, Cassandra, Gremlin) diretamente para a sua base de dados Cosmos. Ao suportar os protocolos de fio de APIs NoSQL comumente usados, cosmos DB permite-lhe:

* Migrar facilmente a sua aplicação para Cosmos DB preservando partes significativas da sua lógica de aplicação.
* Mantenha a sua aplicação portátil e continue a permanecer em nuvem agnóstica.
* Obtenha um serviço de nuvem totalmente gerido com SLAs líderes da indústria, apoiados financeiramente para as APIs noSQL comuns. 
* Dimensione elasticamente a produção e armazenamento previstos para as suas bases de dados com base na sua necessidade e pague apenas pela produção e armazenamento de que necessita. Isto leva a uma poupança significativa de custos.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Soluções que tiram partido do Azure Cosmos DB

Qualquer [aplicação web, móvel, gaming e IoT](use-cases.md) que precise de lidar com quantidades massivas de dados, leituras e escritos a uma [escala global](distribute-data-globally.md) com tempos de resposta quase reais para uma variedade de dados beneficiará da alta disponibilidade [garantida](https://azure.microsoft.com/support/legal/sla/cosmos-db/)da Cosmos DB, alta produção, baixa latência e consistência inavelmente. Saiba como a Azure Cosmos DB pode ser usada para construir [IoT e telemática,](use-cases.md#iot-and-telematics) [retalho e marketing,](use-cases.md#retail-and-marketing) [jogos](use-cases.md#gaming) e [aplicações web e móveis.](use-cases.md#web-and-mobile-applications)

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre os conceitos fundamentais da Cosmos DB na [chave da distribuição global](distribute-data-globally.md) e [partição](partitioning-overview.md) e [produção a provisionada.](request-units.md)

Introdução ao Azure Cosmos DB com um dos nossos manuais de introdução:

* [Introdução à API SQL do Azure Cosmos DB](create-sql-api-dotnet.md)
* [Começa com a API da Azure Cosmos para a MongoDB](create-mongodb-nodejs.md)
* [Introdução à API Cassandra do Azure Cosmos DB](create-cassandra-dotnet.md)
* [Introdução à API do Gremlin do Azure Cosmos DB](create-graph-dotnet.md)
* [Introdução à API de Tabela do Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/)
