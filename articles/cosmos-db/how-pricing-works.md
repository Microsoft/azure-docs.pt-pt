---
title: Modelo de preços da Azure Cosmos DB
description: Este artigo explica o modelo de preços da Azure Cosmos DB e como simplifica a sua gestão de custos e planeamento de custos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 3a2f7f8edfab635d1c59ce61b60b7c5b5311b283
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2020
ms.locfileid: "85513346"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Modelo de preços no Azure Cosmos DB 

O modelo de preços do Azure Cosmos DB simplifica a gestão e o planeamento de custos. Com o Azure Cosmos DB, paga pelo débito aprovisionado e pelo armazenamento consumido.

* **Produção prevista**: A produção proviscizada (também designada por produção reservada) garante um elevado desempenho em qualquer escala. Você especifica a produção (RU/s) que você precisa, e Azure Cosmos DB dedica os recursos necessários para garantir a produção configurada. É cobrado de hora a hora para o rendimento máximo previsto por uma hora.

   > [!NOTE]
   > Como o modelo de produção a provisionado dedica recursos ao seu contentor ou base de dados, será cobrado pelo rendimento a provisionado, mesmo que não faça nenhuma carga de trabalho.

* **Armazenamento consumido**: É faturado uma taxa fixa para a quantidade total de armazenamento (GBs) consumida para os dados e os índices por uma hora.

A produção prevista, especificada como [Unidades de Pedido](request-units.md) por segundo (RU/s), permite-lhe ler ou escrever dados em contentores ou bases de dados. Pode [provistirá a produção numa base de dados ou num contentor.](set-throughput.md) Com base nas suas necessidades de carga de trabalho, pode escalar a produção para cima/para baixo a qualquer momento. O preço do Azure Cosmos DB é elástico e é proporcional à produção que configura numa base de dados ou num recipiente. Os valores mínimos de produção e armazenamento e os incrementos de escala proporcionam uma gama completa de preços vs. espectro de elasticidade a todos os segmentos de clientes, desde recipientes de pequena escala a recipientes em larga escala. Cada base de dados ou contentor é faturado de hora a hora para o rendimento previsto nas unidades de 100 RU/s, com um mínimo de 400 RU/s, e armazenamento consumido em GBs. Ao contrário do rendimento previsto, o armazenamento é faturado numa base de consumo. Isto é, não tens de reservar nenhum armazém com antecedência. É cobrado apenas pelo armazenamento que consome.

Para mais informações, consulte a [página de preços da Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) e [compreenda a sua conta DB Azure Cosmos](understand-your-bill.md).

O modelo de preços em Azure Cosmos DB é consistente em todas as APIs. Para saber mais, consulte [como o modelo de preços da Azure Cosmos DB é rentável para os clientes.](total-cost-ownership.md) Há uma produção mínima necessária numa base de dados ou num contentor para garantir que as SLAs podem aumentar ou diminuir a produção provisida em ~$6 por cada 100 RU/s.

Atualmente, o preço mínimo para ambas as bases de dados e para a produção baseada em contentores é de ~$24/mês (consulte a [página de preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter informações mais recentes. Se a sua carga de trabalho utilizar vários contentores, pode ser otimizado para o custo utilizando o nível de base de dados, porque o nível de base de dados permite-lhe ter qualquer número de contentores numa base de dados que partilhem a produção entre os contentores. O quadro que se segue resume a produção prevista e os custos para diferentes entidades:

|**Entidade**  | **Custo mínimo de & de produção** |**Incrementos de escala & custo** |**Âmbito de provisionamento** |
|---------|---------|---------|-------|
|Base de Dados    | 400 RU/s (~$24/mês)    | 100 RU/s (~$6/mês)   |O rendimento é reservado para a base de dados e é partilhado por contentores dentro da base de dados |
|Contentor     | 400 RU/s (~$24/mês)    | 100 RU/s (~$6/mês)  |A produção está reservada para um recipiente específico |

Como mostrado na tabela anterior, a produção mínima em Azure Cosmos DB começa a um preço de ~$24/mês. Se começar com o rendimento mínimo e escalar ao longo do tempo para suportar as suas cargas de trabalho de produção, os seus custos aumentarão suavemente, nos incrementos de ~$6/mês. O modelo de preços em Azure Cosmos DB é elástico e há um aumento suave ou diminuição do preço à medida que escala ou desce.

## <a name="try-azure-cosmos-db-for-free"></a>Experimentar o Azure Cosmos DB gratuitamente 

A Azure Cosmos DB oferece várias opções para programadores gratuitamente. As opções incluem:

* **Conta gratuita Azure**: A Azure oferece um [nível gratuito](https://azure.microsoft.com/free/) que lhe dá $200 em créditos Azure durante os primeiros 30 dias e uma quantidade limitada de serviços gratuitos por 12 meses. Para obter mais informações, veja [Conta gratuita do Azure](../cost-management-billing/manage/avoid-charges-free-account.md). Azure Cosmos DB faz parte da conta gratuita do Azure. Especificamente para a Azure Cosmos DB, esta conta gratuita oferece armazenamento de 5 GB e 400 RUs de produção provisida para todo o ano. 

* **Experimente gratuitamente Azure Cosmos DB**: A Azure Cosmos DB oferece uma experiência limitada no tempo, utilizando a tentativa Azure Cosmos DB para contas gratuitas. Pode criar uma conta DB Azure Cosmos, criar base de dados e coleções e executar uma aplicação de amostra utilizando os Quickstarts e tutoriais. Pode executar o pedido de amostra sem subscrever uma conta Azure ou usar o seu cartão de crédito. [Experimente a Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) oferece a Azure Cosmos DB por um mês, com a capacidade de renovar a sua conta várias vezes.

* **Azure Cosmos DB emulador**: O emulador Azure Cosmos DB proporciona um ambiente local que emudece o serviço DB Azure Cosmos para fins de desenvolvimento. O emulador é oferecido sem custos e com alta fidelidade ao serviço na nuvem. Utilizando o emulador DB da Azure Cosmos, pode desenvolver e testar as suas aplicações localmente, sem criar uma subscrição Azure ou incorrer em quaisquer custos. Pode desenvolver as suas aplicações utilizando o emulador localmente antes de entrar em produção. Depois de estar satisfeito com a funcionalidade da aplicação contra o emulador, pode mudar para usar a conta DB do Azure Cosmos na nuvem e economizar significativamente no custo. Para obter mais informações sobre o emulador, consulte [o Uso do Azure Cosmos DB para desenvolvimento e teste](local-emulator.md) de artigo para obter mais detalhes.

## <a name="pricing-with-reserved-capacity"></a>Preços com capacidade reservada

A [capacidade reservada da](cosmos-db-reserved-capacity.md) Azure Cosmos ajuda-o a economizar dinheiro, pré-pagando os recursos DB da Azure Cosmos durante um ano ou três anos. Pode reduzir significativamente os seus custos com compromissos iniciais de um ano ou três anos e poupar entre 20 a 65% de descontos em comparação com o preço normal. A capacidade reservada da Azure Cosmos ajuda-o a baixar os custos, pré-pagando a produção provisida (RU/s) por um período de um ano ou três anos e obtém um desconto sobre a produção provisida. 

A capacidade reservada proporciona um desconto de faturação e não afeta o estado de runtime dos recursos do Azure Cosmos DB. A capacidade reservada está disponível de forma consistente para todas as APIs, que incluem MongoDB, Cassandra, SQL, Gremlin e Azure Tables e todas as regiões em todo o mundo. Você pode saber mais sobre a capacidade reservada em [Prepay para recursos DB Azure Cosmos com](cosmos-db-reserved-capacity.md) artigo de capacidade reservada e comprar capacidade reservada a partir do [portal Azure.](https://portal.azure.com/)

## <a name="next-steps"></a>Passos seguintes

Pode aprender mais sobre a otimização dos custos dos seus recursos DB Azure Cosmos nos seguintes artigos:

* Saiba [mais otimizar para desenvolvimento e testes](optimize-dev-test.md)
* Saiba mais sobre [compreender a sua conta de DB da Azure Cosmos](understand-your-bill.md)
* Saiba mais sobre [otimizar o custo de produção](optimize-cost-throughput.md)
* Saiba mais sobre [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo das leituras e dos escritos](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das consultas](optimize-cost-queries.md)
* Saiba mais sobre [otimizar o custo das contas cosmos multi-regiões](optimize-cost-regions.md)
* Conheça a [capacidade reservada da Azure Cosmos DB](cosmos-db-reserved-capacity.md)
* Saiba mais sobre [o Emulador DB da Azure Cosmos](local-emulator.md)
