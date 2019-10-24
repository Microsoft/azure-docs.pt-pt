---
title: Modelo de preço do Azure Cosmos DB
description: Este artigo explica o modelo de preços do Azure Cosmos DB e como ele simplifica o gerenciamento de custos e o planejamento de custos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 0e0adef2728ef75dc67f02fdf38b6638965df62f
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756862"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Modelo de preços no Azure Cosmos DB 

O modelo de preços do Azure Cosmos DB simplifica o planejamento e o gerenciamento de custos. Com Azure Cosmos DB, você paga pela taxa de transferência provisionada e pelo armazenamento que você consome.

* **Taxa de transferência provisionada**: a taxa de transferência provisionada (também chamada de taxa de transferência reservada) garante alto desempenho em qualquer escala. Você especifica a taxa de transferência (RU/s) de que precisa e Azure Cosmos DB dedica os recursos necessários para garantir a taxa de transferência configurada. Você é cobrado por hora para obter a taxa de transferência máxima provisionada para uma determinada hora.

   > [!NOTE]
   > Como o modelo de taxa de transferência provisionado dedica recursos ao contêiner ou banco de dados, você será cobrado pela taxa de transferência provisionada mesmo que não execute nenhuma carga de trabalho.

* **Armazenamento consumido**: você será cobrado como uma taxa fixa para a quantidade total de armazenamento (GB) consumida para os dados e os índices de uma determinada hora.

A taxa de transferência provisionada, especificada como [unidades de solicitação](request-units.md) por segundo (ru/s), permite que você leia ou grave dados em contêineres ou bancos de dados. Você pode [provisionar a taxa de transferência em um banco de dados ou em um contêiner](set-throughput.md). Com base nas suas necessidades de carga de trabalho, você pode dimensionar a taxa de transferência para cima/para baixo a qualquer momento. Azure Cosmos DB preço é elástico e é proporcional à taxa de transferência que você configura em um banco de dados ou em um contêiner. Os valores mínimos de taxa de transferência e armazenamento e os incrementos de escala fornecem uma gama completa de preço versus espectro de elasticidade para todos os segmentos de clientes, de pequena escala a contêineres de grande escala. Cada banco de dados ou um contêiner é cobrado por hora para a taxa de transferência provisionada nas unidades de 100 RU/s, com um mínimo de 400 RU/s e armazenamento consumido em GBs. Ao contrário da taxa de transferência provisionada, o armazenamento é cobrado de acordo com o consumo. Ou seja, você não precisa reservar nenhum armazenamento com antecedência. Você será cobrado apenas pelo armazenamento que consumir.

Para obter mais informações, consulte a [página de preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) e [noções básicas sobre sua fatura de Azure Cosmos DB](understand-your-bill.md).

O modelo de preços no Azure Cosmos DB é consistente em todas as APIs. Para saber mais, consulte [como Azure Cosmos DB modelo de preços é econômico para os clientes](total-cost-ownership.md). Há uma taxa de transferência mínima necessária em um banco de dados ou em um contêiner para garantir os SLAs e você pode aumentar ou diminuir a taxa de transferência provisionada em $6 para cada 100 RU/s.

Atualmente, o preço mínimo para o banco de dados e a taxa de transferência baseada em contêiner é de US $24/mês (consulte a [página de preços Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter informações mais recentes. Se sua carga de trabalho usa vários contêineres, ela pode ser otimizada para custos usando a taxa de transferência no nível do banco de dados, pois a taxa de transferência no nível do banco de dados permite que você tenha qualquer número de contêineres em um banco de dados que compartilha a A tabela a seguir resume a taxa de transferência provisionada e os custos para diferentes entidades:

|**Entidade**  | **Custo mínimo de & de produtividade** |**Incrementos de escala & custo** |**Escopo de provisionamento** |
|---------|---------|---------|-------|
|Base de Dados    | 400 RU/s (US $24/mês)    | 100 RU/s (US $6/mês)   |A taxa de transferência é reservada para o banco de dados e é compartilhada por contêineres no banco de dados |
|Contentor     | 400 RU/s (US $24/mês)    | 100 RU/s (US $6/mês)  |A taxa de transferência é reservada para um contêiner específico |

Conforme mostrado na tabela anterior, a taxa de transferência mínima no Azure Cosmos DB começa com um preço de $24/mês. Se você começar com a taxa de transferência mínima e escalar verticalmente ao longo do tempo para dar suporte às suas cargas de trabalho de produção, seus custos aumentarão suavemente, em incrementos de $6/mês. O modelo de preços no Azure Cosmos DB é elástico e há um aumento suave ou diminuição no preço à medida que você escala ou reduz verticalmente.

## <a name="try-azure-cosmos-db-for-free"></a>Experimente o Azure Cosmos DB gratuitamente 

O Azure Cosmos DB oferece várias opções para os desenvolvedores a ele gratuitamente. As opções incluem:

* **Conta gratuita do Azure**: o Azure oferece uma [camada gratuita](https://azure.microsoft.com/free/) que fornece $200 em créditos do Azure pelos primeiros 30 dias e uma quantidade limitada de serviços gratuitos por 12 meses. Para obter mais informações, veja [Conta gratuita do Azure](../billing/billing-avoid-charges-free-account.md). Azure Cosmos DB faz parte da conta gratuita do Azure. Especificamente para Azure Cosmos DB, essa conta gratuita oferece armazenamento de 5 GB e 400 RUs de taxa de transferência provisionada para o ano inteiro. 

* **Experimente o Azure Cosmos DB gratuitamente**: o Azure Cosmos DB oferece uma experiência de tempo limitado usando o try Azure Cosmos DB para contas gratuitas. Você pode criar uma conta de Azure Cosmos DB, criar um banco de dados e coleções e executar um aplicativo de exemplo usando os guias de início rápido e tutoriais. Você pode executar o aplicativo de exemplo sem assinar uma conta do Azure ou usar seu cartão de crédito. [Tente Azure Cosmos DB para ofertas gratuitas](https://azure.microsoft.com/try/cosmosdb/) Azure Cosmos dB por um mês, com a capacidade de renovar sua conta várias vezes.

* **Emulador de Azure Cosmos DB**: o emulador de Azure Cosmos DB fornece um ambiente local que emula o serviço de Azure Cosmos DB para fins de desenvolvimento. O emulador é oferecido sem custo e com alta fidelidade ao serviço de nuvem. Usando o emulador Azure Cosmos DB, você pode desenvolver e testar seus aplicativos localmente, sem criar uma assinatura do Azure ou incorrer em custos. Você pode desenvolver seus aplicativos usando o emulador localmente antes de entrar em produção. Depois que estiver satisfeito com a funcionalidade do aplicativo no emulador, você poderá alternar para o uso da conta de Azure Cosmos DB na nuvem e economizar significativamente o custo. Para obter mais informações sobre o emulador, consulte o artigo [usando Azure Cosmos DB para desenvolvimento e teste](local-emulator.md) para obter mais detalhes.

## <a name="pricing-with-reserved-capacity"></a>Preços com capacidade reservada

Azure Cosmos DB [capacidade reservada](cosmos-db-reserved-capacity.md) ajuda você a economizar dinheiro por meio do pagamento antecipado de Azure Cosmos DB recursos por um ou três anos. Você pode reduzir significativamente os custos com compromissos antecipados de um ou três anos e economizar entre 20-65% de descontos quando comparado ao preço regular. Azure Cosmos DB capacidade reservada ajuda a reduzir os custos ao pagar antecipadamente pela taxa de transferência provisionada (RU/s) por um período de um ano ou três anos, e você obtém um desconto na taxa de transferência provisionada. 

A capacidade reservada fornece um desconto de cobrança e não afeta o estado de tempo de execução de seus recursos de Azure Cosmos DB. A capacidade reservada está disponível de forma consistente para todas as APIs, que inclui o MongoDB, Cassandra, SQL, Gremlin e tabelas do Azure e todas as regiões em todo o mundo. Você pode saber mais sobre a capacidade reservada no [pagamento antecipado para recursos de Azure Cosmos DB com capacidade reservada](cosmos-db-reserved-capacity.md) e comprar capacidade reservada do [portal do Azure](https://portal.azure.com/).

## <a name="next-steps"></a>Passos seguintes

Você pode aprender mais sobre como otimizar os custos de seus Azure Cosmos DB recursos nos seguintes artigos:

* Saiba mais sobre como [otimizar para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre como [entender sua fatura de Azure Cosmos DB](understand-your-bill.md)
* Saiba mais sobre como [otimizar o custo da taxa de transferência](optimize-cost-throughput.md)
* Saiba mais sobre como [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre como [otimizar o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre como [otimizar o custo de consultas](optimize-cost-queries.md)
* Saiba mais sobre como [otimizar o custo de contas de Cosmos de várias regiões](optimize-cost-regions.md)
* Saiba mais sobre [Azure Cosmos DB capacidade reservada](cosmos-db-reserved-capacity.md)
* Saiba mais sobre o [emulador de Azure Cosmos DB](local-emulator.md)
