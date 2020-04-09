---
title: Introdução ao Azure Cosmos DB
description: Saiba mais sobre o Azure Cosmos DB. Esta base de dados de distribuição global com vários modelos foi concebida para baixa latência, escalabilidade elástica e elevada disponibilidade, e fornece suporte nativo para dados NoSQL.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: 6292c3c2d928581d0564fe457342ea7b1e17111c
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983029"
---
# <a name="welcome-to-azure-cosmos-db"></a>Bem-vindo ao Azure Cosmos DB

As aplicações de hoje são necessárias para serem altamente responsivas e sempre online. Para alcançar baixa latência e elevada disponibilidade, as ocorrências destas aplicações precisam de ser implementadas em datacenters próximos dos seus utilizadores. As aplicações precisam responder em tempo real a grandes mudanças de utilização nas horas de ponta, armazenar cada vez mais volumes de dados e disponibilizar estes dados aos utilizadores em milissegundos.

O Azure Cosmos DB é o serviço de base de dados multimodelo distribuído globalmente pela Microsoft. Com um clique de um botão, cosmos DB permite-lhe escalar elástico e independentemente a produção e armazenamento em várias regiões azure em todo o mundo. Você pode escalar elástico sucintamente a entrada e armazenamento, e aproveitar o acesso rápido de dados de um dígito de milissegundousando o seu API favorito, incluindo: SQL, MongoDB, Cassandra, Tabelas ou Gremlin. Cosmos DB fornece [acordos abrangentes de nível](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) de serviço (SLAs) para garantias de entrada, latência, disponibilidade e consistência, algo que nenhum outro serviço de base de dados oferece.

Você pode [Experimentar Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição Azure, gratuitamente e compromissos ou usar o [nível gratuito Azure Cosmos DB](optimize-dev-test.md#azure-cosmos-db-free-tier) para obter uma conta com os primeiros 400 RU/s e 5 GB de armazenamento gratuitos.

> [!div class="nextstepaction"]
> [Experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/)

![O Azure Cosmos DB é um serviço de base de dados de distribuição global da Microsoft com aumento horizontal elástico, baixa latência garantida, cinco modelos de consistência e SLAs abrangentes garantidos](./media/introduction/azure-cosmos-db.png)

## <a name="key-benefits"></a>Principais vantagens

### <a name="turnkey-global-distribution"></a>Distribuição global chave na mão

Cosmos DB permite-lhe construir aplicações altamente responsivas e altamente disponíveis em todo o mundo. Cosmos DB replica de forma transparente os seus dados onde quer que os seus utilizadores estejam, para que os seus utilizadores possam interagir com uma réplica dos dados que lhes são mais próximos.

Cosmos DB permite-lhe adicionar ou remover qualquer uma das regiões Azure à sua conta Cosmos a qualquer momento, com um clique de um botão. Cosmos DB irá replicar perfeitamente os seus dados para todas as regiões associadas à sua conta Cosmos, enquanto a sua aplicação continua altamente disponível, graças às capacidades *multi-homing* do serviço. Para mais informações, consulte o artigo de [distribuição global.](distribute-data-globally.md)

### <a name="always-on"></a>Sempre ligado

Em virtude de uma profunda integração com a infraestrutura Azure e [a replicação transparente multi-master,](global-dist-under-the-hood.md)a Cosmos DB proporciona [uma disponibilidade de 99,999%](high-availability.md) para leituras e escritos. Cosmos DB também lhe fornece a capacidade de invocar programáticamente (ou via Portal) a falha regional da sua conta Cosmos. Esta capacidade ajuda a garantir que a sua aplicação foi concebida para falhar em caso de desastre regional.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>Escalabilidade elástica de produção e armazenamento, em todo o mundo

Projetado com divisória horizontal transparente e replicação multi-master, cosmos DB oferece escalae elástica sem precedentes para suas escritas e leituras, em todo o mundo. Você pode escalar elástico de milhares a centenas de milhões de pedidos/sec em todo o mundo, com uma única chamada API e pagar apenas pela entrada (e armazenamento) que você precisa. Esta capacidade ajuda-o a lidar com picos inesperados nas suas cargas de trabalho sem ter de fornecer demasiado para o pico. Para mais informações, consulte [a divisão em Cosmos DB](partitioning-overview.md), [aprovisionada em recipientes e bases](set-throughput.md)de dados, e a [escalação prevista](scaling-throughput.md)para a produção global .

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>Latência garantida baixa no percentil 99, em todo o mundo

Usando cosmos DB, você pode construir aplicações altamente responsivas, à escala de planetas. Com o seu novo protocolo de replicação multi-master e motor de base de dados sem trincos e [otimizado](index-policy.md)por escritos, a Cosmos DB garante menos de 10 ms de atraso para ambos, lê(indexado) e escreve no percentil 99, em todo o mundo. Esta capacidade permite a ingestão sustentada de dados e consultas rápidas para aplicações altamente responsivas.

### <a name="precisely-defined-multiple-consistency-choices"></a>Escolhas de consistência múltipla seleção precisamente definidas

Ao construir aplicações distribuídas globalmente no Cosmos DB, já não é necessário fazer [trocas extremas entre consistência, disponibilidade, latência e produção.](consistency-levels-tradeoffs.md) O protocolo de replicação multi-master da Cosmos DB foi cuidadosamente concebido para oferecer [cinco opções](consistency-levels.md) - de consistência bem definidas*fortes,* *delimitados,* *sessão,* *prefixo consistente,* e *eventualmente* – para um modelo de programação intuitivo com baixa latência e elevada disponibilidade para a sua aplicação distribuída globalmente.

### <a name="no-schema-or-index-management"></a>Sem esquemaou gestão de índices

Manter o esquema de base de dados e os índices em sincronização com o esquema de uma aplicação é especialmente doloroso para aplicações distribuídas globalmente. Com a Cosmos DB, não precisa de lidar com esquemas ou gestão de índices. O motor da base de dados é totalmente esquema-agnóstico.  Uma vez que não é necessário nenhum esquema e gestão de índices, também não tem de se preocupar com o tempo de inatividade da aplicação enquanto migra os esquemas. Cosmos DB indexa automaticamente todos os dados e serve consultas [rapidamente.](index-policy.md)

### <a name="battle-tested-database-service"></a>Serviço de base de dados testado em batalha

Cosmos DB é um serviço fundamental em Azure. Durante quase uma década, a Cosmos DB tem sido usada por muitos dos produtos da Microsoft para aplicações críticas de missão à escala global, incluindo Skype, Xbox, Office 365, Azure, entre muitos outros. Hoje em dia, a Cosmos DB é um dos serviços de crescimento mais rápido do Azure, utilizado por muitos clientes externos e aplicações críticas de missão que requerem escala elástica, distribuição global chave na mão, replicação multi-master para baixa latência e elevada disponibilidade de leituras e escritos.

### <a name="ubiquitous-regional-presence"></a>Presença regional ubíqua

Cosmos DB está disponível em todas as regiões de Azure em todo o mundo, incluindo 54 regiões mais de nuvem pública, [Azure China 21Vianet](https://www.azure.cn/en-us/), Azure Alemanha, Azure Government, e Azure Government for Department of Defense (DoD). Veja a [presença regional da Cosmos DB.](regional-presence.md)

### <a name="secure-by-default-and-enterprise-ready"></a>Seguro por padrão e pronto para a empresa

Cosmos DB é certificado para uma [ampla gama de padrões de conformidade.](compliance.md) Além disso, todos os dados em Cosmos DB são encriptados em repouso e em movimento. Cosmos DB fornece autorização de nível de linha e adere a rigorosos padrões de segurança.

### <a name="significant-tco-savings"></a>Economias significativas de TCO

Uma vez que a Cosmos DB é um serviço totalmente gerido, já não precisa de gerir e operar implementações complexas de multidatacenter e upgrades do software da sua base de dados, pagar pelo suporte, licenciamento ou operações ou ter de fornecer a sua base de dados para o pico da carga de trabalho. Para mais informações, consulte [O custo da Otimize com cosmos DB](total-cost-ownership.md).

### <a name="industry-leading-comprehensive-slas"></a>SLAs abrangentes líderes da indústria

Cosmos DB é o primeiro e único serviço a oferecer [SLAs abrangentes líderes](https://azure.microsoft.com/support/legal/sla/cosmos-db/) do setor, abrangendo 99,999% de elevada disponibilidade, leitura e escrita latência no percentil 99, rendimento garantido e consistência.

### <a name="globally-distributed-operational-analytics-and-ai-with-natively-built-in-apache-spark"></a>Análise operacional distribuída globalmente e IA com apache spark nativamente incorporado

Você pode executar [Spark](spark-connector.md) diretamente em dados armazenados em Cosmos DB. Esta capacidade permite-lhe fazer análises operacionais de baixa latência à escala global sem afetar as cargas de trabalho transacionais que operam diretamente contra a Cosmos DB. Para mais informações, consulte a [análise operacional distribuída globalmente.](lambda-architecture.md)

### <a name="develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis"></a>Desenvolver aplicações em Cosmos DB utilizando apis populares de software open source (OSS)

Cosmos DB oferece uma escolha de APIs para trabalhar com os seus dados armazenados na sua base de dados Cosmos. Por predefinição, [pode utilizar o SQL](how-to-sql-query.md) (uma API central) para consultar a sua base de dados Cosmos. Cosmos DB também implementa APIs para [Cassandra,](cassandra-introduction.md) [MongoDB,](mongodb-introduction.md) [Gremlin](graph-introduction.md) e [Azure Table Storage.](table-introduction.md) Pode indicar os condutores de clientes (e ferramentas) para o NoSQL (por exemplo, MongoDB, Cassandra, Gremlin) diretamente para a sua base de dados Cosmos. Ao apoiar os protocolos de arame de APIs NoSQL comumente usados, cosmos DB permite-lhe:

* Emigre facilmente a sua aplicação para cosmos DB, preservando partes significativas da sua lógica de aplicação.
* Mantenha a sua aplicação portátil e continue a permanecer agnóstica em nuvem.
* Obtenha um serviço de nuvem totalmente gerido com sLAs líderes da indústria, apoiados financeiramente para as APIs noSQL comuns. 
* Dimensione eelasticmente a entrada e armazenamento aprovisionados para as suas bases de dados com base na sua necessidade e pague apenas pela entrada e armazenamento de que necessita. Isto leva a uma significativa poupança de custos.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Soluções que tiram partido do Azure Cosmos DB

Qualquer [aplicação web, móvel, gaming e IoT](use-cases.md) que precise lidar com quantidades massivas de dados, lê e escreve à [escala global](distribute-data-globally.md) com tempos de resposta quase reais para uma variedade de dados beneficiará da elevada disponibilidade [garantida](https://azure.microsoft.com/support/legal/sla/cosmos-db/)da Cosmos DB, alta produção, baixa latência e consistência insugável. Saiba como o Azure Cosmos DB pode ser usado para construir [IoT e telemática,](use-cases.md#iot-and-telematics) [retalho e marketing,](use-cases.md#retail-and-marketing) [jogos](use-cases.md#gaming) e [aplicações web e móveis.](use-cases.md#web-and-mobile-applications)

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre os conceitos fundamentais da Cosmos DB [distribuição](distribute-data-globally.md) global e [partição](partitioning-overview.md) e [produção aprovisionada.](request-units.md)

Introdução ao Azure Cosmos DB com um dos nossos manuais de introdução:

* [Introdução à API SQL do Azure Cosmos DB](create-sql-api-dotnet.md)
* [Inicie-se com a API da Azure Cosmos DB para o MongoDB](create-mongodb-nodejs.md)
* [Introdução à API Cassandra do Azure Cosmos DB](create-cassandra-dotnet.md)
* [Introdução à API do Gremlin do Azure Cosmos DB](create-graph-dotnet.md)
* [Introdução à API de Tabela do Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/)
