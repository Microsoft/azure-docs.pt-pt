---
title: Modelo de preços da Azure Cosmos DB
description: Este artigo explica o modelo de preços da Azure Cosmos DB e como simplifica a sua gestão de custos e planeamento de custos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 573fc4fac413ceed50246bc6fb8df1d9db021c94
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247476"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Modelo de preços no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O modelo de preços do Azure Cosmos DB simplifica a gestão e o planeamento de custos. Com a Azure Cosmos DB, paga pelas operações que realiza na base de dados e pelo armazenamento consumido pelos seus dados.

- **Operações de base de dados**: A forma como é cobrado pelas suas operações de base de dados depende do tipo de conta Azure Cosmos que está a utilizar.

  - **Produção provisão**: [A produção proviscizada](set-throughput.md) (também designada por produção reservada) proporciona um elevado desempenho em qualquer escala. Especifica a produção de que necessita nas [Unidades](request-units.md) de Pedido por segundo (RU/s), e a Azure Cosmos DB dedica os recursos necessários para fornecer a produção configurada. Pode [provistirá a produção numa base de dados ou num contentor.](set-throughput.md) Com base nas suas necessidades de carga de trabalho, pode escalar a produção para cima/para baixo a qualquer momento ou utilizar [a autoescala](provision-throughput-autoscale.md) (embora exista um rendimento mínimo exigido numa base de dados ou num recipiente para garantir as SLAs). É cobrado de hora a hora para o rendimento máximo previsto por uma hora.

   > [!NOTE]
   > Como o modelo de produção a provisionado dedica recursos ao seu contentor ou base de dados, será cobrado pelo rendimento que a provisionou, mesmo que não execute nenhuma carga de trabalho.

  - **Serverless**: Em modo [sem servidor,](serverless.md) não é necessário prever qualquer produção na criação de recursos na sua conta Azure Cosmos. No final do seu período de faturação, é cobrado a quantidade de Unidades de Pedido que foi consumida pelas suas operações de base de dados.

- **Armazenamento**: É faturada uma taxa fixa para a quantidade total de armazenamento (em BB) consumida pelos seus dados e índices durante uma hora. O armazenamento é cobrado numa base de consumo, por isso não tem de reservar qualquer armazenamento com antecedência. É cobrado apenas pelo armazenamento que consome.

O modelo de preços em Azure Cosmos DB é consistente em todas as APIs. Para mais informações, consulte a [página de preços da Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/), [compreendendo a sua conta de DB Azure Cosmos](understand-your-bill.md) e [como o modelo de preços DB da Azure Cosmos é rentável para os clientes.](total-cost-ownership.md)

Se implementar a sua conta DB Azure Cosmos para uma região não governamental nos EUA, existe um preço mínimo tanto para a base de dados como para a produção baseada em contentores no modo de produção forerado. Não há preço mínimo no modo sem servidor. O preço varia consoante a região que está a usar, consulte a [página de preços da Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter informações mais recentes sobre preços.

## <a name="try-azure-cosmos-db-for-free"></a>Experimentar o Azure Cosmos DB gratuitamente

A Azure Cosmos DB oferece muitas opções para os desenvolvedores para ele gratuitamente. As opções incluem:

* **Nível livre Azure Cosmos DB**: O nível livre Azure Cosmos DB facilita o início, o desenvolvimento e o teste das suas aplicações, ou até mesmo executar pequenas cargas de trabalho de produção gratuitamente. Quando o nível livre estiver ativado numa conta, obterá os primeiros 400 RU/s e 5 GB de armazenamento na conta gratuitamente, durante o tempo de vida da conta. Pode ter até uma conta de nível livre por subscrição Azure e deve optar pela criação da conta. Para começar, [crie uma nova conta no portal Azure com nível gratuito ou](create-cosmosdb-resources-portal.md) utilize um modelo [ARM](./manage-with-templates.md#free-tier).

* **Conta gratuita Azure**: A Azure oferece um [nível gratuito](https://azure.microsoft.com/free/) que lhe dá $200 em créditos Azure durante os primeiros 30 dias e uma quantidade limitada de serviços gratuitos por 12 meses. Para obter mais informações, veja [Conta gratuita do Azure](../cost-management-billing/manage/avoid-charges-free-account.md). Azure Cosmos DB faz parte da conta gratuita do Azure. Especificamente para a Azure Cosmos DB, esta conta gratuita oferece armazenamento de 25 GB e 400 RU/s de produção provisida para todo o ano.

* **Experimente gratuitamente Azure Cosmos DB**: A Azure Cosmos DB oferece uma experiência limitada no tempo, utilizando a tentativa Azure Cosmos DB para contas gratuitas. Pode criar uma conta DB Azure Cosmos, criar base de dados e coleções e executar uma aplicação de amostra utilizando os Quickstarts e tutoriais. Pode executar o pedido de amostra sem subscrever uma conta Azure ou usar o seu cartão de crédito. [Experimente a Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) oferece a Azure Cosmos DB por um mês, com a capacidade de renovar a sua conta várias vezes.

* **Azure Cosmos DB emulador**: O emulador Azure Cosmos DB proporciona um ambiente local que emudece o serviço DB Azure Cosmos para fins de desenvolvimento. O emulador é oferecido sem custos e com alta fidelidade ao serviço na nuvem. Utilizando o emulador DB da Azure Cosmos, pode desenvolver e testar as suas aplicações localmente, sem criar uma subscrição Azure ou incorrer em quaisquer custos. Pode desenvolver as suas aplicações utilizando o emulador localmente antes de entrar em produção. Depois de estar satisfeito com a funcionalidade da aplicação contra o emulador, pode mudar para usar a conta DB do Azure Cosmos na nuvem e economizar significativamente no custo. Para obter mais informações sobre o emulador, consulte [o Uso do Azure Cosmos DB para desenvolvimento e teste](local-emulator.md) de artigo para obter mais detalhes.

## <a name="pricing-with-reserved-capacity"></a>Preços com capacidade reservada

A [capacidade reservada da](cosmos-db-reserved-capacity.md) Azure Cosmos ajuda-o a economizar dinheiro ao utilizar o modo de produção a provisionado, pré-pagando os recursos DB da Azure Cosmos durante um ano ou três anos. Pode reduzir significativamente os seus custos com compromissos iniciais de um ano ou três anos e poupar entre 20 a 65% de descontos em comparação com o preço normal. A capacidade reservada da Azure Cosmos ajuda-o a baixar os custos, pré-pagando a produção provisida (RU/s) por um período de um ano ou três anos e obtém um desconto sobre a produção provisida. 

A capacidade reservada proporciona um desconto de faturação e não afeta o estado de runtime dos recursos do Azure Cosmos DB. A capacidade reservada está disponível de forma consistente para todas as APIs, que incluem MongoDB, Cassandra, SQL, Gremlin e Azure Tables e todas as regiões em todo o mundo. Você pode saber mais sobre a capacidade reservada em [Prepay para recursos DB Azure Cosmos com](cosmos-db-reserved-capacity.md) artigo de capacidade reservada e comprar capacidade reservada a partir do [portal Azure.](https://portal.azure.com/)

## <a name="next-steps"></a>Passos seguintes

Pode aprender mais sobre a otimização dos custos dos seus recursos DB Azure Cosmos nos seguintes artigos:

* Saiba [mais otimizar para desenvolvimento e testes](optimize-dev-test.md)
* Saiba mais sobre [compreender a sua conta de DB da Azure Cosmos](understand-your-bill.md)
* Saiba mais sobre [otimizar o custo de produção](optimize-cost-throughput.md)
* Saiba mais sobre [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo das leituras e dos escritos](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das consultas](./optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das contas cosmos multi-regiões](optimize-cost-regions.md)
* Conheça a [capacidade reservada da Azure Cosmos DB](cosmos-db-reserved-capacity.md)
* Saiba mais sobre [o Emulador DB da Azure Cosmos](local-emulator.md)