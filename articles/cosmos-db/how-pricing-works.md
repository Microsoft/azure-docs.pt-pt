---
title: Modelo de preços do Azure Cosmos DB
description: Este artigo explica o modelo de preços da Azure Cosmos DB e como simplifica a sua gestão de custos e planeamento de custos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 7efae8fb3c00868e2740eac2d4d5bcb3c82f663a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75977546"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Modelo de preços no Azure Cosmos DB 

O modelo de preços do Azure Cosmos DB simplifica a gestão e o planeamento de custos. Com o Azure Cosmos DB, paga pelo débito aprovisionado e pelo armazenamento consumido.

* **Entrada provisionada**: A entrada aprovisionada (também chamada de entrada reservada) garante um elevado desempenho em qualquer escala. Especifica a entrada (RU/s) de que necessita, e a Azure Cosmos DB dedica os recursos necessários para garantir a entrada configurada. Você é cobrado por hora para o máximo previsto para uma determinada hora.

   > [!NOTE]
   > Uma vez que o modelo de entrada provisionado dedica recursos ao seu recipiente ou base de dados, será cobrado pela entrada prevista, mesmo que não execute nenhuma carga de trabalho.

* **Armazenamento consumido**: É cobrado uma taxa fixa para a quantidade total de armazenamento (GBs) consumido para dados e os índices durante uma determinada hora.

A entrada aprovisionada, especificada como [Unidades](request-units.md) de Pedido por segundo (RU/s), permite-lhe ler ou escrever dados em contentores ou bases de dados. Pode fornecer entrada numa base de [dados ou num recipiente.](set-throughput.md) Com base nas suas necessidades de carga de trabalho, pode escalar a entrada para cima/para baixo a qualquer momento. Os preços do Azure Cosmos DB são elásticos e são proporcionais à entrada que configura numa base de dados ou num recipiente. Os valores mínimos de entrada e armazenamento e os incrementos de escala proporcionam uma gama completa de espectro de preços vs. elasticidade a todos os segmentos de clientes, desde recipientes de pequena escala a recipientes em larga escala. Cada base de dados ou contentor é faturado numa base horária para a entrada aprovisionada nas unidades de 100 RU/s, com um mínimo de 400 RU/s, e armazenamento consumido em GBs. Ao contrário do que é previsto, o armazenamento é faturado numa base de consumo. Ou seja, não tens de reservar nenhum armazenamento com antecedência. Só é cobrado pelo armazenamento que consome.

Para mais informações, consulte a página de [preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) e compreenda a sua conta De DB [Azure Cosmos.](understand-your-bill.md)

O modelo de preços em Azure Cosmos DB é consistente em todas as APIs. Para saber mais, veja como o modelo de [preços da Azure Cosmos DB é rentável para os clientes.](total-cost-ownership.md) Existe uma entrada mínima necessária numa base de dados ou num recipiente para garantir que as SLAs podem aumentar ou diminuir a entrada prevista em $6 por cada 100 RU/s.

Atualmente, o preço mínimo tanto para a base de dados como para a entrada em contentores é de 24 dólares por mês (ver página de preços do [Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter informações mais recentes. Se a sua carga de trabalho utilizar vários recipientes, pode ser otimizado para o custo utilizando a entrada do nível da base de dados, uma vez que a entrada de nível de base de dados permite-lhe ter qualquer número de contentores numa base de dados que partilhe a entrada entre os contentores. O quadro seguinte resume o que é previsto e os custos para as diferentes entidades:

|**Entidade**  | **Custo mínimo de & de entrada** |**Incrementos de escala & custo** |**Âmbito de provisionamento** |
|---------|---------|---------|-------|
|Base de Dados    | 400 RU/s ($24/mês)    | 100 RU/s ($6/mês)   |A entrada é reservada para a base de dados e é partilhada por contentores dentro da base de dados |
|Contentor     | 400 RU/s ($24/mês)    | 100 RU/s ($6/mês)  |A entrada é reservada para um recipiente específico |

Como mostrado na tabela anterior, o rendimento mínimo em Azure Cosmos DB começa a um preço de $24/mês. Se começar com a entrada mínima e aumentar ao longo do tempo para suportar as suas cargas de trabalho de produção, os seus custos aumentarão suavemente, nos incrementos de $6/mês. O modelo de preços em Azure Cosmos DB é elástico e há um aumento suave ou diminuição do preço à medida que escala para cima ou para baixo.

## <a name="try-azure-cosmos-db-for-free"></a>Experimentar o Azure Cosmos DB gratuitamente 

A Azure Cosmos DB oferece-lhe várias opções gratuitamente para os desenvolvedores. As opções incluem:

* **Conta gratuita Azure**: O Azure oferece um [nível gratuito](https://azure.microsoft.com/free/) que lhe dá 200 dólares em créditos Azure nos primeiros 30 dias e uma quantidade limitada de serviços gratuitos durante 12 meses. Para obter mais informações, veja [Conta gratuita do Azure](../cost-management-billing/manage/avoid-charges-free-account.md). Azure Cosmos DB faz parte da conta gratuita do Azure. Especificamente para o Azure Cosmos DB, esta conta gratuita oferece armazenamento de 5 GB e 400 RUs de entrada prevista para todo o ano. 

* **Experimente o Azure Cosmos DB gratuitamente:** o Azure Cosmos DB oferece uma experiência limitada ao utilizar o Azure Cosmos DB gratuitamente. Pode criar uma conta Azure Cosmos DB, criar base de dados e coleções e executar uma aplicação de amostra utilizando os Quickstarts e tutoriais. Pode executar o pedido de amostra sem subscrever uma conta Azure ou utilizar o seu cartão de crédito. [Experimente o Azure Cosmos DB gratuitamente para](https://azure.microsoft.com/try/cosmosdb/) ofertas gratuitas Azure Cosmos DB por um mês, com a capacidade de renovar a sua conta várias vezes.

* **O emulador Azure Cosmos DB**: O emulador Azure Cosmos DB proporciona um ambiente local que emudece o serviço Azure Cosmos DB para fins de desenvolvimento. O emulador é oferecido sem custos e com alta fidelidade ao serviço de nuvem. Utilizando o emulador Azure Cosmos DB, pode desenvolver e testar as suas aplicações localmente, sem criar uma subscrição Azure ou incorrer em quaisquer custos. Pode desenvolver as suas aplicações utilizando o emulador localmente antes de entrar em produção. Depois de se encontrar satisfeito com a funcionalidade da aplicação contra o emulador, pode mudar para utilizar a conta Azure Cosmos DB na nuvem e economizar significativamente no custo. Para mais informações sobre o emulador, consulte a Utilização do [Azure Cosmos DB para o desenvolvimento e teste](local-emulator.md) do artigo para mais detalhes.

## <a name="pricing-with-reserved-capacity"></a>Preços com capacidade reservada

A [capacidade reservada](cosmos-db-reserved-capacity.md) da Azure Cosmos DB ajuda-o a economizar dinheiro ao pagar os recursos DB da Azure Cosmos durante um ano ou três anos. Pode reduzir significativamente os seus custos com compromissos iniciais de um ano ou três anos e economizar entre 20 a 65% de descontos quando comparado com os preços regulares. A capacidade reservada da Azure Cosmos DB ajuda-o a reduzir os custos ao pagar o produto (RU/s) por um período de um ano ou três anos e obtém um desconto na entrada prevista. 

A capacidade reservada proporciona um desconto de faturação e não afeta o estado de runtime dos recursos do Azure Cosmos DB. A capacidade reservada está disponível de forma consistente para todas as APIs, que inclui MongoDB, Cassandra, SQL, Gremlin e Azure Tables e todas as regiões em todo o mundo. Você pode aprender mais sobre a capacidade reservada em [Prepay para os recursos Da BD Azure Cosmos com](cosmos-db-reserved-capacity.md) artigo de capacidade reservado e comprar capacidade reservada no [portal Azure](https://portal.azure.com/).

## <a name="next-steps"></a>Passos seguintes

Pode aprender mais sobre otimizar os custos dos seus recursos Azure Cosmos DB nos seguintes artigos:

* Saiba mais sobre [otimização para desenvolvimento e testes](optimize-dev-test.md)
* Saiba mais sobre [compreender a sua conta de DB Azure Cosmos](understand-your-bill.md)
* Saiba mais sobre [otimização do custo de entrada](optimize-cost-throughput.md)
* Saiba mais sobre [otimização do custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo das leituras e dos escritos](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das consultas](optimize-cost-queries.md)
* Saiba mais sobre [otimizar o custo das contas cosmos multi-regiões](optimize-cost-regions.md)
* Conheça a capacidade reservada do [Azure Cosmos DB](cosmos-db-reserved-capacity.md)
* Conheça o [Emulador DB da Azure Cosmos](local-emulator.md)
