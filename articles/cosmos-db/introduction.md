---
title: Introdução ao Azure Cosmos DB
description: Saiba mais sobre o Azure Cosmos DB. Esta base de dados de distribuição global com vários modelos foi concebida para baixa latência, escalabilidade elástica e elevada disponibilidade, e fornece suporte nativo para dados NoSQL.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: f322a240835d86697ae18c984700f22ca7b00145
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882187"
---
# <a name="welcome-to-azure-cosmos-db"></a>Bem-vindo ao Azure Cosmos DB

Os aplicativos de hoje precisam ser altamente responsivos e sempre online. Para obter baixa latência e alta disponibilidade, as instâncias desses aplicativos precisam ser implantadas em data centers próximos aos seus usuários. Os aplicativos precisam responder em tempo real a grandes alterações de uso em horários de pico, armazenar volumes de dados cada vez maiores e disponibilizar esses dados para os usuários em milissegundos.

Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. Com um clique de um botão, o Cosmos DB permite dimensionar de forma elástica e independente a taxa de transferência e o armazenamento em qualquer número de regiões do Azure em todo o mundo. Você pode dimensionar de forma elástica a taxa de transferência e o armazenamento e aproveitar o acesso a dados rápido e com um único dígito de milissegundos usando sua API favorita, incluindo SQL, MongoDB, Cassandra, Tables ou Gremlin. O Cosmos DB fornece SLAs ( [contratos de nível de serviço](https://aka.ms/acdbsla) ) abrangentes para garantias de taxa de transferência, latência, disponibilidade e consistência, algo que nenhum outro serviço de banco de dados oferece.

Pode [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição do Azure, sem encargos nem compromissos.

> [!div class="nextstepaction"]
> [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/)

![O Azure Cosmos DB é um serviço de base de dados de distribuição global da Microsoft com aumento horizontal elástico, baixa latência garantida, cinco modelos de consistência e SLAs abrangentes garantidos](./media/introduction/azure-cosmos-db.png)

## <a name="key-benefits"></a>Principais Vantagens

### <a name="turnkey-global-distribution"></a>Distribuição global chave na mão

Cosmos DB permite que você crie aplicativos altamente responsivos e altamente disponíveis em todo o mundo. Cosmos DB Replica de forma transparente seus dados onde quer que os usuários estejam, para que os usuários possam interagir com uma réplica dos dados mais próximos deles.

Cosmos DB permite adicionar ou remover qualquer uma das regiões do Azure para sua conta do cosmos a qualquer momento, com um clique de botão. Cosmos DB replicará diretamente seus dados para todas as regiões associadas à sua conta do cosmos enquanto seu aplicativo continua a estar altamente disponível, graças aos recursos de *hospedagem múltipla* do serviço. Para obter mais informações, consulte o artigo [distribuição global](distribute-data-globally.md) .

### <a name="always-on"></a>Sempre Ativo

Em virtude da integração profunda com a infraestrutura do Azure e [replicação de vários mestres transparentes](global-dist-under-the-hood.md), Cosmos DB fornece [99,999% de alta disponibilidade](high-availability.md) para leituras e gravações. Cosmos DB também fornece a capacidade de programaticamente (ou via portal) invocar o failover regional da sua conta do cosmos. Esse recurso ajuda a garantir que seu aplicativo seja projetado para failover no caso de desastre regional.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>Escalabilidade elástica da produtividade e do armazenamento, em todo o mundo

Projetado com particionamento horizontal transparente e replicação de vários mestres, a Cosmos DB oferece escalabilidade elástica sem precedentes para suas gravações e leituras, em todo o mundo. Você pode escalar verticalmente de milhares a centenas de milhões de solicitações/s em todo o mundo, com uma única chamada à API e pagar apenas pela taxa de transferência (e pelo armazenamento) de que você precisa. Essa funcionalidade ajuda você a lidar com picos inesperados em suas cargas de trabalho sem precisar provisionar para o pico. Para obter mais informações, consulte [particionamento em Cosmos DB](partitioning-overview.md), [taxa de transferência provisionada em contêineres e bancos de dados](set-throughput.md)e [dimensionamento da produtividade provisionada globalmente](scaling-throughput.md).

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>Baixa latência garantida em 99 º percentil, em todo o mundo

Usando Cosmos DB, você pode criar aplicativos de escala altamente responsivos e de planeta. Com seu próprio protocolo de replicação de vários mestres e [mecanismo de banco de dados com otimização de gravação](index-policy.md)e sem travas, Cosmos DB garante menos de 10 ms de latências para leituras (indexadas) e gravações no 99 º percentil, em todo o mundo. Esse recurso permite a ingestão sustentada de dados e consultas mais rápidas para aplicativos altamente responsivos.

### <a name="precisely-defined-multiple-consistency-choices"></a>Precisamente definidas, várias opções de consistência

Ao criar aplicativos distribuídos globalmente no Cosmos DB, você não precisa mais fazer [compensações extremas entre consistência, disponibilidade, latência e taxa de transferência](consistency-levels-tradeoffs.md). O protocolo de replicação de vários mestres de Cosmos DB foi cuidadosamente projetado para oferecer [cinco opções de consistência bem definidas](consistency-levels.md) - *forte*, desatualização *limitada*, *sessão*, *prefixo consistente*e *eventual* — para um modelo de programação intuitivo com baixa latência e alta disponibilidade para seu aplicativo distribuído globalmente.

### <a name="no-schema-or-index-management"></a>Nenhum gerenciamento de esquema ou índice

Manter o esquema de banco de dados e os índices em sincronia com o esquema de um aplicativo é especialmente trabalhoso para aplicativos distribuídos globalmente. Com o Cosmos DB, você não precisa lidar com o gerenciamento de esquema ou índice. O mecanismo de banco de dados é totalmente independente de esquema.  Como nenhum gerenciamento de esquema e índice é necessário, você também não precisa se preocupar com o tempo de inatividade do aplicativo durante a migração de esquemas. Cosmos DB [indexa automaticamente todos os dados](index-policy.md) e atende as consultas rapidamente.

### <a name="battle-tested-database-service"></a>Serviço de banco de dados testado por batalha

Cosmos DB é um serviço fundamental no Azure. Por quase uma década, a Cosmos DB foi usada por muitos produtos da Microsoft para aplicativos de missão crítica em escala global, incluindo Skype, Xbox, Office 365, Azure e muitos outros. Hoje, Cosmos DB é um dos serviços de crescimento mais rápido no Azure, usado por muitos clientes externos e aplicativos de missão crítica que exigem escala elástica, distribuição global completa, replicação de vários mestres para baixa latência e alta disponibilidade de ambos leituras e gravações.

### <a name="ubiquitous-regional-presence"></a>Presença regional onipresente

O Cosmos DB está disponível em todas as regiões do Azure em todo o mundo, incluindo mais de 54 regiões na nuvem pública, [Azure China 21vianet](https://www.azure.cn/en-us/), Azure Alemanha, Azure governamental e Azure governamental para o departamento de defesa (DoD). Consulte a [presença regional do cosmos DB](regional-presence.md).

### <a name="secure-by-default-and-enterprise-ready"></a>Seguro por padrão e pronto para empresas

A Cosmos DB é certificada por uma [ampla gama de padrões de conformidade](compliance.md). Além disso, todos os dados no Cosmos DB são criptografados em repouso e em movimento. O Cosmos DB fornece autorização em nível de linha e segue os padrões de segurança estritos.

### <a name="significant-tco-savings"></a>Economia significativa de TCO

Como Cosmos DB é um serviço totalmente gerenciado, você não precisa mais gerenciar e operar implantações complexas de datacenter e atualizações de seu software de banco de dados, pagar pelo suporte, licenciamento ou operações ou ter que provisionar seu banco de dados para a carga de trabalho de pico. Para obter mais informações, consulte [otimizar o custo com Cosmos DB](total-cost-ownership.md).

### <a name="industry-leading-comprehensive-slas"></a>SLAs abrangentes líderes do setor

Cosmos DB é o primeiro e único serviço a oferecer [SLAs abrangentes líderes do setor](https://azure.microsoft.com/support/legal/sla/cosmos-db/) que abrangem 99,999% de alta disponibilidade, latência de leitura e gravação no 99 º percentil, taxa de transferência garantida e consistência.

### <a name="globally-distributed-operational-analytics-and-ai-with-natively-built-in-apache-spark"></a>Análise operacional distribuída globalmente e ia com Apache Spark interno nativamente

Você pode executar o [Spark](spark-connector.md) diretamente nos dados armazenados no cosmos DB. Esse recurso permite que você faça análises operacionais de baixa latência em escala global sem afetar as cargas de trabalho transacionais operando diretamente com Cosmos DB. Para obter mais informações, consulte [análise operacional distribuída globalmente](lambda-architecture.md).

### <a name="develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis"></a>Desenvolva aplicativos em Cosmos DB usando APIs de software livre (OSS) populares

O Cosmos DB oferece uma opção de APIs para trabalhar com seus dados armazenados em seu Cosmos Database. Por padrão, [você pode usar o SQL](how-to-sql-query.md) (uma API principal) para consultar o banco de dados Cosmos. Cosmos DB também implementa APIs para [Cassandra](cassandra-introduction.md), [MongoDB](mongodb-introduction.md), [Gremlin](graph-introduction.md) e [armazenamento de tabelas do Azure](table-introduction.md). Você pode apontar drivers de cliente (e ferramentas) para o NoSQL usado com frequência (por exemplo, MongoDB, Cassandra, Gremlin) diretamente para seu banco de dados Cosmos. Ao oferecer suporte aos protocolos de conexão das APIs NoSQL usadas com frequência, Cosmos DB permite que você:

* Migre facilmente seu aplicativo para Cosmos DB enquanto preserva partes significativas da lógica do seu aplicativo.
* Mantenha seu aplicativo portátil e continue a permanecer independente do fornecedor da nuvem.
* Obtenha um serviço de nuvem totalmente gerenciado com SLAs líderes do setor e com suporte financeiro para as APIs NoSQL comuns. 
* Dimensione de forma elástica a taxa de transferência e o armazenamento provisionados para seus bancos de dados com base na sua necessidade e pague apenas pela taxa de transferência e pelo armazenamento de que você precisa. Isso leva a uma economia de custo significativa.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Soluções que tiram partido do Azure Cosmos DB

Qualquer [aplicativo Web, móvel, de jogos e de IOT](use-cases.md) que precise lidar com grandes quantidades de dados, leituras e gravações em uma [escala global](distribute-data-globally.md) com tempos de resposta quase reais para uma variedade de dados se beneficiará da [alta disponibilidade garantida de Cosmos DB ](https://azure.microsoft.com/support/legal/sla/cosmos-db/), alta taxa de transferência, baixa latência e consistência ajustável. Saiba mais sobre como Azure Cosmos DB pode ser usado para criar [IOT e](use-cases.md#iot-and-telematics) [telecomerciais, varejo e marketing](use-cases.md#retail-and-marketing), [jogos](use-cases.md#gaming) e [aplicativos Web e móveis](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre os principais conceitos do Cosmos DB está pronto para a [distribuição global](distribute-data-globally.md) e o [particionamento](partitioning-overview.md) e a [taxa de transferência provisionada](request-units.md).

Introdução ao Azure Cosmos DB com um dos nossos manuais de introdução:

* [Introdução à API SQL do Azure Cosmos DB](create-sql-api-dotnet.md)
* [Introdução à API do Azure Cosmos DB para MongoDB](create-mongodb-nodejs.md)
* [Introdução à API Cassandra do Azure Cosmos DB](create-cassandra-dotnet.md)
* [Introdução à API do Gremlin do Azure Cosmos DB](create-graph-dotnet.md)
* [Introdução à API de Tabela do Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/)
