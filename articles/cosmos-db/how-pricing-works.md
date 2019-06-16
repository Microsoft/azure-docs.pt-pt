---
title: Modelo de preços do Azure Cosmos DB
description: Este artigo explica o modelo de preços do Azure Cosmos DB e como ela simplifica a gestão de custos e planejamento de custo.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: c9e7afdb8927a2cdada8ae86ebf18a42327e640c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65968947"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Modelo de preços no Azure Cosmos DB 

O modelo de preços do Azure Cosmos DB simplifica o custo de planejamento e gerenciamento. Com o Azure Cosmos DB, paga o débito aprovisionado e o armazenamento que consumir.

* **Aprovisionar o débito**: Débito aprovisionado (também chamado de débito reservado) garante elevado desempenho em qualquer escala. Especifique o débito (RU/s) que precisa e Azure Cosmos DB dedica os recursos necessários para garantir o débito configurado. É cobrado por hora para o débito aprovisionado máximo para uma determinada hora.

   > [!NOTE]
   > Porque o modelo de débito aprovisionado dedica recursos para o seu contentor ou a base de dados, será cobrado para o débito aprovisionado, mesmo se não executar quaisquer cargas de trabalho.

* **Consumido armazenamento**: É-lhe cobrada uma tarifa fixa para a quantidade total de armazenamento (GBs) consumido para dados e os índices para uma determinada hora.

Débito aprovisionado, especificado como [unidades de pedido](request-units.md) por segundo (RU/s), permite-lhe ler ou escrever dados em bases de dados ou de contentores. Pode [débito de aprovisionar num banco de dados ou um contentor](set-throughput.md). Com base nas necessidades da sua carga de trabalho, pode dimensionar o débito para cima/para baixo em qualquer altura. Os preços do Azure Cosmos DB é elástico e é proporcional ao débito que configurar num banco de dados ou um contentor. Os valores mínimos de armazenamento e débito e os incrementos de dimensionamento fornecem uma gama completa de preço versus o espectro de elasticidade a todos os segmentos de clientes, de pequena escala para os contentores em grande escala. Cada base de dados ou um contentor é cobrado numa base horária para o débito aprovisionado nas unidades de 100 RU/s, com um mínimo de 400 RU/s e o armazenamento consumido em GB. Ao contrário do débito aprovisionado, o armazenamento é cobrado numa base de consumo. Ou seja, não tem de reservar qualquer armazenamento com antecedência. É cobrado apenas pelo armazenamento que consumir.

Para obter mais informações, consulte a [página de preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) e [entender a sua fatura do Azure Cosmos DB](understand-your-bill.md).

O modelo de preços no Azure Cosmos DB é consistente em todas as APIs. Para obter mais informações, consulte [como do Azure Cosmos DB modelo de preços é econômico para os clientes](total-cost-ownership.md). Existe um débito mínimo necessário num banco de dados ou um contentor para garantir que os SLAs e pode aumentar ou diminuir o débito aprovisionado por US $6 para cada 100 RU/s.

Atualmente, o preço mínimo para a base de dados e o débito baseadas em contentores é 24 us $/ mês (consulte a [página de preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) para informações mais recentes. Se a sua carga de trabalho utiliza vários contentores, pode ser otimizado para custo através da utilização de débito de nível de base de dados porque o débito de nível de base de dados, pode ter qualquer número de contentores numa base de dados a taxa de transferência entre os contentores de partilha. A tabela seguinte resume o débito aprovisionado e os custos para entidades diferentes:

|**Entidade**  | **Débito mínimo e o custo** |**Incrementos de dimensionamento e o custo** |**Âmbito de aprovisionamento** |
|---------|---------|---------|-------|
|Base de Dados    | 400 RU/s (24 us $/ mês)    | 100 RU/s (6 de US $/ mês)   |Taxa de transferência está reservada para a base de dados e é partilhada por contentores na base de dados |
|Contentor     | 400 RU/s (24 us $/ mês)    | 100 RU/s (6 de US $/ mês)  |Taxa de transferência está reservada para um contentor específico |

Conforme mostrado na tabela anterior, o débito mínimo no Azure Cosmos DB é iniciado por um preço de US $24/ mês. Se for iniciado com o débito mínimo e o dimensionamento ao longo do tempo para suportar as cargas de trabalho de produção, os seus custos aumentará sem problemas, em incrementos de 6 us $/ mês. O modelo de preços no Azure Cosmos DB é elástico e há uma suave aumento ou diminuição no preço como aumentar ou reduzir verticalmente.

## <a name="try-azure-cosmos-db-for-free"></a>Experimentar o Azure Cosmos DB gratuitamente 

O Azure Cosmos DB oferece várias opções para os desenvolvedores-lo gratuitamente. As opções incluem:

* **Conta gratuita do Azure**: O Azure oferece um [escalão gratuito](https://azure.microsoft.com/free/) que fornece a $200 em créditos do Azure para os primeiros 30 dias e uma quantidade limitada de serviços gratuitos durante 12 meses. Para obter mais informações, veja [Conta gratuita do Azure](../billing/billing-avoid-charges-free-account.md). O Azure Cosmos DB é uma parte da conta gratuita do Azure. Especificamente para o Azure Cosmos DB, nesta conta gratuita oferece 5 GB de armazenamento e 400 RUs do débito aprovisionado no ano inteiro. 

* **Experimente o Azure Cosmos DB gratuitamente**: O Azure Cosmos DB oferece um tempo limitado experiência com o Experimente o Azure Cosmos DB para contas gratuitas. Pode criar uma conta do Azure Cosmos DB, criar a base de dados e coleções e executar um aplicativo de exemplo ao utilizar os inícios rápidos e tutoriais. Pode executar o aplicativo de exemplo sem subscrever uma conta do Azure ou com cartão de crédito. [Experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) oferece o Azure Cosmos DB durante um mês, com a capacidade de renovar a sua conta de qualquer número de vezes.

* **Emulador do Azure Cosmos DB**: Emulador do Azure Cosmos DB fornece um ambiente local que emula o serviço do Azure Cosmos DB para fins de desenvolvimento. Emulador é disponibilizado sem custos e com alta fidelidade para o serviço em nuvem. Utilizar o emulador do Azure Cosmos DB, pode desenvolver e testar seus aplicativos localmente, sem criar uma subscrição do Azure ou incorrer em custos. Pode desenvolver as suas aplicações ao utilizar o emulador localmente antes de entrar em produção. Depois que estiver satisfeito com a funcionalidade do aplicativo contra o emulador, pode passar a utilizar a conta do Azure Cosmos DB na cloud e significativamente Poupe no custo. Para obter mais informações sobre o emulador, consulte [utilizando o Azure Cosmos DB para desenvolvimento e teste](local-emulator.md) artigo para obter mais detalhes.

## <a name="pricing-with-reserved-capacity"></a>Preços com capacidade de reserva

O Azure Cosmos DB [capacidade de reserva](cosmos-db-reserved-capacity.md) ajuda a economizar dinheiro ao pagar previamente para recursos do Azure Cosmos DB para um ano ou três anos. Significativamente pode reduzir os custos com um ano ou três anos compromissos imediatos e guardar entre 20 e 65% descontos em comparação com os preços normais. O Azure Cosmos DB reservado ajuda de capacidade, que reduza os custos ao pagar previamente para o débito aprovisionado (RU/s) durante um período de um ano ou três anos e obtenha um desconto no débito aprovisionado. 

Capacidade de reserva fornece um desconto de faturação e não afeta o estado de tempo de execução dos seus recursos do Azure Cosmos DB. Capacidade de reserva está disponível consistentemente para todas as APIs, que inclui MongoDB, Cassandra, SQL, Gremlin e as tabelas do Azure e todas as regiões em todo o mundo. Pode saber mais sobre a capacidade de reserva no [pré-pagamento para recursos do Azure Cosmos DB com a capacidade de reserva](cosmos-db-reserved-capacity.md) artigo e comprar capacidade de reserva da [portal do Azure](https://portal.azure.com/).

## <a name="next-steps"></a>Passos Seguintes

Pode saber mais sobre como otimizar os custos para os seus recursos do Azure Cosmos DB nos seguintes artigos:

* Saiba mais sobre [otimizar para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre [entender a sua fatura do Azure Cosmos DB](understand-your-bill.md)
* Saiba mais sobre [otimizar o custo de débito](optimize-cost-throughput.md)
* Saiba mais sobre [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo de consultas](optimize-cost-queries.md)
* Saiba mais sobre [otimizar o custo de contas do Cosmos de várias regiões](optimize-cost-regions.md)
* Saiba mais sobre [capacidade de reserva do Azure Cosmos DB](cosmos-db-reserved-capacity.md)
* Saiba mais sobre [emulador do Azure Cosmos DB](local-emulator.md)
