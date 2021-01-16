---
title: Otimização para desenvolvimento e testes em Azure Cosmos DB
description: Este artigo explica como a Azure Cosmos DB oferece múltiplas opções para o desenvolvimento e teste do serviço gratuitamente.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 3ddae808fbb2e3dcfe20909c8b3d0c5a20bb04bd
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247527"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Otimizar o desenvolvimento e testar o custo no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Este artigo descreve as diferentes opções de utilização do Azure Cosmos DB para desenvolvimento e testes sem custos, bem como técnicas para otimizar custos em contas de desenvolvimento ou teste.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Emulador Azure Cosmos DB (versão localmente transferível)

[Azure Cosmos DB emulador](local-emulator.md) é uma versão local que imita o serviço de nuvem DB Azure Cosmos. Pode escrever e testar código que utilize as APIs DB do Azure Cosmos mesmo que não tenha ligação à rede e sem incorrer em custos. O emulador Azure Cosmos DB proporciona um ambiente local para fins de desenvolvimento com alta fidelidade ao serviço na nuvem. Pode desenvolver e testar a sua aplicação localmente, sem criar uma subscrição do Azure. Quando estiver pronto para implementar a sua aplicação na nuvem, atualize o fio de ligação para ligar ao ponto final DB do Azure Cosmos na nuvem, não são necessárias outras modificações. Também pode configurar um oleoduto CI/CD com a tarefa de construção [do emulador Azure Cosmos DB](tutorial-setup-ci-cd.md) em Azure DevOps para realizar testes. Pode começar visitando o artigo [do emulador Azure Cosmos DB.](local-emulator.md)

## <a name="azure-cosmos-db-free-tier"></a>Azure Cosmos DB free tier

O nível livre de DB da Azure Cosmos facilita o início, o desenvolvimento e o teste das suas aplicações, ou até mesmo executar pequenas cargas de trabalho de produção gratuitamente. Quando o nível livre estiver ativado numa conta, obterá os primeiros 400 RU/s e 5 GB de armazenamento na conta gratuitamente. Também pode criar uma base de dados de produção partilhada com 25 contentores que partilham 400 RU/s ao nível da base de dados, todos cobertos por nível livre (bases de dados de produção partilhadas limite 5 numa conta de nível livre). Ao utilizar o nível livre, se forrou uma base de dados partilhada com um rendimento mínimo de 400 RU/s, todos os contentores nessa base de dados podem partilhar o resultado. Quaisquer novas bases de dados com produção partilhada ou contentores com produção dedicada são faturadas ao preço regular.

> [!NOTE]
> O nível livre só está disponível no modo de produção a provisionado.

O nível livre dura indefinidamente para a vida útil da conta e vem com todos os [benefícios e características](introduction.md#key-benefits) de uma conta DB Azure Cosmos regular, incluindo armazenamento ilimitado e produção (RU/s), SLAs, alta disponibilidade, distribuição global chave na mão em todas as regiões do Azure, e muito mais. Pode ter até uma conta de nível livre por subscrição Azure e deve optar pela criação da conta. Para começar, [crie uma nova conta no portal Azure com nível gratuito ou](create-cosmosdb-resources-portal.md) utilize um modelo [ARM](./manage-with-templates.md#free-tier). Veja a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter mais informações.

## <a name="try-azure-cosmos-db-for-free"></a>Experimentar o Azure Cosmos DB gratuitamente

[Experimente gratuitamente AZure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) é uma experiência gratuita que lhe permite experimentar com Azure Cosmos DB na nuvem sem se inscrever numa conta Azure ou usar o seu cartão de crédito. As contas de Try Azure Cosmos DB estão disponíveis por tempo limitado, atualmente 30 dias. Pode renová-los a qualquer momento. Experimente as contas DB da Azure Cosmos facilita a avaliação do Azure Cosmos DB, construa e teste uma aplicação ou utilize os Quickstarts ou tutoriais. Também pode criar uma demonstração, realizar testes de unidade ou até mesmo criar uma conta multi-região e executar uma aplicação nela sem incorrer em quaisquer custos. Numa conta de Try Azure Cosmos DB, pode ter uma base de dados de produção partilhada com um máximo de 25 contentores e 20.000 RU/s de produção, ou um contentor com até 5000 RU/s. Para começar, consulte [Azure Cosmos DB para](https://azure.microsoft.com/try/cosmosdb/) página gratuita.

## <a name="azure-free-account"></a>Conta gratuita do Azure

O Azure Cosmos DB está incluído na conta gratuita do [Azure,](https://azure.microsoft.com/free)que oferece créditos e recursos da Azure gratuitamente durante um determinado período de tempo. Especificamente para a Azure Cosmos DB, esta conta gratuita oferece armazenamento de 25 GB e 400 RUs de produção provisida para todo o ano. Esta experiência permite que qualquer desenvolvedor teste facilmente as características da Azure Cosmos DB ou integre-a com outros serviços Azure a custo zero. Com a conta gratuita do Azure, recebes um crédito de 200 dólares para gastar nos primeiros 30 dias. Não será cobrado, mesmo que comece a usar os serviços até que opte por fazer upgrade. Para começar, visite a página [de conta gratuita do Azure.](https://azure.microsoft.com/free)

## <a name="azure-cosmos-db-serverless"></a>Azure Cosmos DB sem servidor

[O Azure Cosmos DB sem servidor](serverless.md) permite-lhe utilizar a sua conta Azure Cosmos de forma baseada no consumo, onde só é cobrado pelas Unidades de Pedido consumidas pelas suas operações de base de dados e pelo armazenamento consumido pelos seus dados. Não existe uma carga mínima envolvida ao utilizar o Azure Cosmos DB no modo sem servidor. Como elimina o conceito de capacidade a provisionada, é mais adequado para atividades de desenvolvimento ou teste especificamente quando a sua base de dados está inativa na maior parte do tempo.

## <a name="use-shared-throughput-databases"></a>Utilizar bases de dados de produção partilhadas

Numa [base de dados de produção partilhada,](set-throughput.md#set-throughput-on-a-database)todos os contentores dentro da base de dados partilham o rendimento previsto (RU/s) da base de dados. Por exemplo, se forre uma base de dados com 400 RU/s e tenha quatro contentores, os quatro contentores partilharão os 400 RU/s. Num ambiente de desenvolvimento ou teste, em que cada recipiente pode ser acedido com menos frequência e, assim, exigir um mínimo inferior ao mínimo de 400 RU/s, colocar contentores numa base de dados de produção partilhada pode ajudar a otimizar os custos.

Por exemplo, suponha que a sua conta de desenvolvimento ou teste tenha quatro contentores. Se criar quatro recipientes com produção dedicada (no mínimo 400 RU/s), o seu TOTAL RU/s será de 1600 RU/s. Em contrapartida, se criar uma base de dados de produção partilhada (mínimo 400 RU/s) e colocar os seus contentores lá, o seu TOTAL RU/s será de apenas 400 RU/s. Em geral, as bases de dados de produção partilhadas são ótimas para cenários em que não é necessário um rendimento garantido em nenhum recipiente individual.  Saiba mais sobre [bases de dados de produção partilhadas.](set-throughput.md#set-throughput-on-a-database)

## <a name="next-steps"></a>Passos seguintes

Pode começar com a utilização do emulador ou das contas gratuitas da Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre [compreender a sua conta de DB da Azure Cosmos](understand-your-bill.md)
* Saiba mais sobre [o Azure Cosmos DB sem servidor](serverless.md)
* Saiba mais sobre [otimizar o custo de produção](optimize-cost-throughput.md)
* Saiba mais sobre [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo das leituras e dos escritos](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das consultas](./optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das contas da Azure Cosmos em várias regiões](optimize-cost-regions.md)