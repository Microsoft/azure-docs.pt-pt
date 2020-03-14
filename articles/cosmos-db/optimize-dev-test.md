---
title: Otimização para desenvolvimento e testes em Azure Cosmos DB
description: Este artigo explica como o Azure Cosmos DB oferece várias opções de desenvolvimento e teste do serviço gratuitamente.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: d2ca7b7e4b637802df6a78c2493e3cc088f09881
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246685"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Otimizar o custo de desenvolvimento e teste no Azure Cosmos DB

Este artigo descreve as diferentes opções de utilização do Azure Cosmos DB para desenvolvimento e teste gratuitamente de custos, bem como técnicas para otimizar os custos em contas de desenvolvimento ou teste.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Emulador Azure Cosmos DB (versão transferível localmente)

[O emulador Azure Cosmos DB](local-emulator.md) é uma versão local transferível que imita o serviço de nuvem Azure Cosmos DB. Pode escrever e testar o código que utiliza as APIs DB do Azure Cosmos, mesmo que não tenha ligação à rede e sem incorrer em quaisquer custos. O emulador Azure Cosmos DB proporciona um ambiente local para fins de desenvolvimento com alta fidelidade ao serviço de nuvem. Pode desenvolver e testar a sua aplicação localmente, sem criar uma subscrição Azure. Quando estiver pronto para implementar a sua aplicação para a nuvem, atualize a cadeia de ligação para ligar ao ponto final do Azure Cosmos DB na nuvem, não são necessárias outras modificações. Também pode configurar um oleoduto CI/CD com a tarefa de construção de [emulador Azure Cosmos DB](tutorial-setup-ci-cd.md) em Azure DevOps para realizar testes. Você pode começar visitando o artigo de [emulador Azure Cosmos DB.](local-emulator.md)

## <a name="azure-cosmos-db-free-tier"></a>Azure Cosmos DB nível livre 
O nível livre azure Cosmos DB facilita o início, o desenvolvimento e o teste das suas aplicações, ou até mesmo executar pequenas cargas de produção gratuitamente. Quando o nível livre estiver ativado numa conta, obterá os primeiros 400 RU/s e 5 GB de armazenamento na conta gratuitamente. Também pode criar uma base de dados de entrada partilhada com 25 contentores que partilham 400 RU/s ao nível da base de dados, todos cobertos por nível livre (limite 5 bases de dados de entrada partilhada sem fim numa conta de nível livre). O nível livre dura indefinidamente durante toda a vida da conta e vem com todos os [benefícios e características](introduction.md#key-benefits) de uma conta regular da Azure Cosmos DB, incluindo armazenamento ilimitado e produção (RU/s), SLAs, alta disponibilidade, distribuição global chave na mão em todas as regiões do Azure, e muito mais. Pode ter até uma conta de nível livre por subscrição do Azure e deve optar por entrar na criação da conta. Para começar, [crie uma nova conta com nível livre habilitado.](create-cosmosdb-resources-portal.md) Veja a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter mais informações. 

## <a name="try-azure-cosmos-db-for-free"></a>Experimentar o Azure Cosmos DB gratuitamente

[Experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) é uma experiência gratuita que permite experimentar com o Azure Cosmos DB na nuvem sem se inscrever para uma conta Azure ou usar o seu cartão de crédito. As contas Try Azure Cosmos DB estão disponíveis por um tempo limitado, atualmente 30 dias. Pode renová-los a qualquer momento. Experimente as contas DB da Azure Cosmos facilita a avaliação do Azure Cosmos DB, construa e teste uma aplicação ou use os Quickstarts ou tutoriais. Também pode criar uma demonstração, realizar testes unitários ou até mesmo criar uma conta multi-região e executar uma aplicação sem incorrer em quaisquer custos. Numa conta Try Azure Cosmos DB, pode ter uma base de dados de entrada partilhada com um máximo de 25 contentores e 20.000 RU/s de entrada, ou um recipiente com até 5000 RU/s. Para começar, consulte [Try Azure Cosmos DB para](https://azure.microsoft.com/try/cosmosdb/) página gratuita.

## <a name="azure-free-account"></a>Conta gratuita azure

O Azure Cosmos DB está incluído na [conta gratuita azure,](https://azure.microsoft.com/free)que oferece créditos e recursos Azure gratuitamente durante um determinado período de tempo. Especificamente para o Azure Cosmos DB, esta conta gratuita oferece armazenamento de 5 GB e 400 RUs de entrada prevista para todo o ano. Esta experiência permite a qualquer desenvolvedor testar facilmente as características do Azure Cosmos DB ou integrá-la com outros serviços Azure a custo zero. Com a conta gratuita do Azure, recebes um crédito de 200 dólares para gastar nos primeiros 30 dias. Não será cobrado, mesmo que comece a utilizar os serviços até optar por fazer upgrade. Para começar, visite a página [de conta gratuita do Azure.](https://azure.microsoft.com/free)

## <a name="use-shared-throughput-databases"></a>Utilize bases de dados de entrada partilhadas

Numa base de [dados de entradas partilhadas,](set-throughput.md#set-throughput-on-a-database)todos os contentores dentro da base de dados partilham o fornecimento (RU/s) da base de dados. Por exemplo, se fornecer uma base de dados com 400 RU/s e tiver quatro contentores, todos os quatro recipientes partilharão os 400 RU/s. Num ambiente de desenvolvimento ou de teste, em que cada recipiente pode ser acedido com menos frequência e, portanto, requer menos do que o mínimo de 400 RU/s, colocar recipientes numa base de dados de entradas partilhadas pode ajudar a otimizar os custos. 

Por exemplo, suponha que a sua conta de desenvolvimento ou teste tem quatro contentores. Se criar quatro recipientes com entrada dedicada (mínimo de 400 RU/s), o seu total de RU/s será de 1600 RU/s. Em contrapartida, se criar uma base de dados de produção partilhada (mínimo de 400 RU/s) e colocar os seus contentores lá, o seu total de RU/s será de apenas 400 RU/s. Em geral, as bases de dados de entrada partilhadas são ótimas para cenários em que não é necessário obter uma entrada garantida em qualquer recipiente individual.  Saiba mais sobre bases de [dados de entrada partilhadas.](set-throughput.md#set-throughput-on-a-database)

## <a name="next-steps"></a>Passos seguintes

Você pode começar com o emulador ou as contas gratuitas do Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre [Otimização para desenvolvimento e testes](optimize-dev-test.md)
* Saiba mais sobre [compreender a sua conta de DB Azure Cosmos](understand-your-bill.md)
* Saiba mais sobre [otimização do custo de entrada](optimize-cost-throughput.md)
* Saiba mais sobre [otimização do custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo das leituras e dos escritos](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das consultas](optimize-cost-queries.md)
* Saiba mais sobre [otimizar o custo das contas multi-regiões da Azure Cosmos](optimize-cost-regions.md)

