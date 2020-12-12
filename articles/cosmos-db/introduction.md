---
title: Introdução ao Azure Cosmos DB
description: Saiba mais sobre o Azure Cosmos DB. Esta base de dados de distribuição global com vários modelos foi concebida para baixa latência, escalabilidade elástica e elevada disponibilidade, e fornece suporte nativo para dados NoSQL.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2020
ms.openlocfilehash: 2dd60d1962734f7a4264587fd1b5b0d2a03c9ff0
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359749"
---
# <a name="welcome-to-azure-cosmos-db"></a>Bem-vindo ao Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

As aplicações atuais têm de ter uma capacidade de resposta elevada e estar sempre online. Para obter baixa latência e elevada disponibilidade, as instâncias destas aplicações têm de ser implementadas em datacenters próximos dos seus utilizadores. As aplicações têm de responder em tempo real a grandes alterações de utilização em horas de pico, armazenar volumes de dados cada vez maiores e disponibilizar esses dados a utilizadores em milissegundos.

Azure Cosmos DB é uma base de dados NoSQL totalmente gerida para o desenvolvimento de aplicações modernas. Tempos de resposta milissegundos de um dígito e escalabilidade automática e instantânea, velocidade de garantia em qualquer escala. A continuidade do negócio é assegurada com disponibilidade [apoiada pelo SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) e segurança de nível empresarial. O desenvolvimento de aplicações é mais rápido e produtivo graças à distribuição de dados multi-regiões chave na mão em qualquer parte do mundo, APIs de código aberto e SDKs para línguas populares. Como um serviço totalmente gerido, a Azure Cosmos DB tira a administração da base de dados das suas mãos com gestão automática, atualizações e remendos. Também lida com a gestão da capacidade com opções de escala sem servidor e automáticas que respondem às necessidades da aplicação para corresponder à capacidade com a procura.

Você pode [experimentar Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição Azure, gratuitamente e compromissos ou usar o [nível gratuito Azure Cosmos DB](optimize-dev-test.md#azure-cosmos-db-free-tier) para obter uma conta com os primeiros 400 RU/s e 5 GB de armazenamento gratuito.

> [!div class="nextstepaction"]
> [Experimente Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/)

:::image type="content" source="./media/introduction/azure-cosmos-db.png" alt-text="Azure Cosmos DB é uma base de dados NoSQL totalmente gerida para o desenvolvimento de aplicações modernas." border="false":::

## <a name="key-benefits"></a>Principais vantagens

### <a name="guaranteed-speed-at-any-scale"></a>Velocidade garantida em qualquer escala

Ganhe uma velocidade e saída incomparáveis [apoiada pelo SLA,](https://azure.microsoft.com/support/legal/sla/cosmos-db) acesso global rápido e elasticidade instantânea.

- Acesso em tempo real com leitura rápida e escrita latências a nível global, e produção e consistência tudo apoiado por [SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db)
- Várias regiões escrevem e distribuição de dados para qualquer região de Azure com o clique de um botão.
- Armazenamento e produção de escalas e armazenamento de forma independente e elástica em qualquer região de Azure – mesmo durante rajadas de tráfego imprevisíveis – para escala ilimitada em todo o mundo.

### <a name="simplified-application-development"></a>Desenvolvimento simplificado de aplicações

Construa rapidamente com APIs de código aberto, múltiplos SDKs, dados sem esquemas e análises sem ETL sobre dados operacionais.

- Profundamente integrado com os principais serviços Azure utilizados no desenvolvimento moderno (nativo da nuvem), incluindo Funções Azure, IoT Hub, AKS (Serviço Azure Kubernetes), Serviço de Aplicações, entre outros.
- Escolha entre várias APIs de base de dados, incluindo a API nativa Core (SQL), API para MongoDB, Cassandra API, Gremlin API e API de Tabela.
- Construa aplicativos em Core (SQL) API usando os idiomas à sua escolha com SDKs para .NET, Java, Node.js e Python. Ou a sua escolha de motoristas para qualquer uma das outras APIs da base de dados.
- Execute análises sem ETL sobre os dados operacionais quase em tempo real armazenados em Azure Cosmos DB com Azure Synapse Analytics.
- Alterar o feed facilita a rastreio e a gestão de alterações nos contentores da base de dados e cria eventos desencadeados com Funções Azure.
- O serviço de esquemas da Azure Cosmos DB indexa automaticamente todos os seus dados, independentemente do modelo de dados, à entrega de consultas rápidas.

### <a name="mission-critical-ready"></a>Missão crítica pronta

Garantia de continuidade do negócio, 99,999% de disponibilidade e segurança ao nível da empresa para cada aplicação.

- A Azure Cosmos DB oferece um conjunto abrangente de [SLAs,](https://azure.microsoft.com/support/legal/sla/cosmos-db) incluindo disponibilidade líder na indústria em todo o mundo.
- Distribua facilmente os dados para qualquer região do Azure com replicação automática de dados. Desfrute de um tempo de inatividade zero com gravações multi-regiões ou RPO 0 ao utilizar uma consistência forte.
- Desfrute de encriptação de nível empresarial em repouso com chaves auto-geridas.
- O controlo de acesso baseado em funções Azure mantém os seus dados seguros e oferece um controlo afinado.

### <a name="fully-managed-and-cost-effective"></a>Totalmente gerido e rentável

Gestão de bases de dados de ponta a ponta, com escala sem servidor e escala automática correspondente à sua aplicação e às necessidades de TCO

- Serviço de base de dados totalmente gerido. Automático, sem toque, manutenção, remendos e atualizações, poupando tempo e dinheiro aos desenvolvedores.
- Opções rentáveis para cargas de trabalho imprevisíveis ou esporádicas de qualquer tamanho ou escala, permitindo que os desenvolvedores arranquem facilmente sem ter que planear ou gerir a capacidade.
- O modelo serverless oferece um serviço automático e responsivo para gerir as rajadas de tráfego a pedido.
- A potência provisão de escala automática e automaticamente e instantaneamente escala a capacidade para cargas de trabalho imprevisíveis, mantendo as [SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db).

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Soluções que tiram partido do Azure Cosmos DB

Qualquer [aplicação web, móvel, gaming e IoT](use-cases.md) que precise de lidar com quantidades massivas de dados, leituras e escritos a uma [escala global](distribute-data-globally.md) com tempos de resposta quase reais para uma variedade de dados beneficiará da alta disponibilidade [garantida](https://azure.microsoft.com/support/legal/sla/cosmos-db/)da Cosmos DB, alta produção, baixa latência e consistência inavelmente. Saiba como a Azure Cosmos DB pode ser usada para construir [IoT e telemática,](use-cases.md#iot-and-telematics) [retalho e marketing,](use-cases.md#retail-and-marketing) [jogos](use-cases.md#gaming) e [aplicações web e móveis.](use-cases.md#web-and-mobile-applications)

## <a name="next-steps"></a>Passos seguintes

Introdução ao Azure Cosmos DB com um dos nossos manuais de introdução:

- [Introdução à API SQL do Azure Cosmos DB](create-sql-api-dotnet.md)
- [Começa com a API da Azure Cosmos para a MongoDB](create-mongodb-nodejs.md)
- [Introdução à API Cassandra do Azure Cosmos DB](create-cassandra-dotnet.md)
- [Introdução à API do Gremlin do Azure Cosmos DB](create-graph-dotnet.md)
- [Introdução à API de Tabela do Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/)