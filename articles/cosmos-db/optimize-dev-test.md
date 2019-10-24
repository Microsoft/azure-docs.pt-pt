---
title: Otimizando para desenvolvimento e teste no Azure Cosmos DB
description: Este artigo explica como a Azure Cosmos DB oferece várias opções de desenvolvimento e teste do serviço gratuitamente.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 992d165d323aab79bb7b5475aa396d4432691530
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754901"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Otimize o custo de desenvolvimento e teste no Azure Cosmos DB

Este artigo descreve as diferentes opções para usar Azure Cosmos DB para desenvolvimento e teste gratuitamente.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Emulador de Azure Cosmos DB (versão com download local)

[Azure Cosmos DB emulador](local-emulator.md) é uma versão local que pode ser baixada, que imita o serviço de nuvem Azure Cosmos DB. Você pode escrever e testar o código que usa as APIs Azure Cosmos DB mesmo que você não tenha nenhuma conexão de rede e sem incorrer em custos. O emulador de Azure Cosmos DB fornece um ambiente local para fins de desenvolvimento com alta fidelidade ao serviço de nuvem. Você pode desenvolver e testar seu aplicativo localmente, sem criar uma assinatura do Azure. Quando você estiver pronto para implantar seu aplicativo na nuvem, atualize a cadeia de conexão para se conectar ao ponto de extremidade Azure Cosmos DB na nuvem, nenhuma outra modificação será necessária. Você também pode [configurar um pipeline de CI/CD com a tarefa Azure Cosmos DB Emulator](tutorial-setup-ci-cd.md) Build no Azure DevOps para executar testes. Você pode começar visitando o artigo [Azure Cosmos DB Emulator](local-emulator.md) .

## <a name="try-azure-cosmos-db-for-free"></a>Experimente o Azure Cosmos DB gratuitamente

[Experimente Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) é uma experiência gratuita que permite criar bancos de dados e coleções e experimentar Azure Cosmos DB na nuvem. Você não precisa se inscrever no Azure nem pagar nenhum custo. As contas do try Azure Cosmos DB estão disponíveis por um período limitado, atualmente 30 dias. Você pode renová-los a qualquer momento. Experimente Azure Cosmos DB contas facilita a avaliação Azure Cosmos DB, a criação e o teste de um aplicativo usando os guias de início rápido ou os tutoriais. Você pode criar uma demonstração ou executar testes de unidade sem incorrer em nenhum custo. Ao usar o try Azure Cosmos DB para contas gratuitas, você pode avaliar os recursos premium de Azure Cosmos DB gratuitamente, incluindo distribuição global, SLAs e modelos de consistência prontos para uso. Você pode criar um banco de dados com um máximo de 25 contêineres de Cosmos do Azure e 10.000 RU/s de taxa de transferência. Você pode executar o aplicativo de exemplo sem assinar uma conta do Azure ou usar seu cartão de crédito. Com o try Azure Cosmos DB gratuitamente, você pode criar uma conta do Azure Cosmos de várias regiões e executar um aplicativo nela em apenas alguns minutos. Para começar, consulte [tentar Azure Cosmos DB para página livre](https://azure.microsoft.com/try/cosmosdb/) .

## <a name="azure-free-account"></a>Conta Gratuita do Azure

Azure Cosmos DB está incluído na [conta gratuita do Azure](https://azure.microsoft.com/free), que oferece créditos e recursos do Azure gratuitamente por um determinado período de tempo. Especificamente para Azure Cosmos DB, essa conta gratuita oferece armazenamento de 5 GB e 400 RUs de taxa de transferência provisionada para o ano inteiro. Essa experiência permite que qualquer desenvolvedor teste facilmente os recursos de Azure Cosmos DB ou integre-os com outros serviços do Azure a um custo zero. Com a conta gratuita do Azure, você recebe um crédito de $200 para gastar nos primeiros 30 dias. Você não será cobrado, mesmo se começar a usar os serviços até optar por atualizar. Para começar, visite a página da [conta gratuita do Azure](https://azure.microsoft.com/free) .

## <a name="next-steps"></a>Passos seguintes

Você pode começar a usar o emulador ou as contas de Azure Cosmos DB gratuitas com os seguintes artigos:

* Saiba mais sobre como [otimizar para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre como [entender sua fatura de Azure Cosmos DB](understand-your-bill.md)
* Saiba mais sobre como [otimizar o custo da taxa de transferência](optimize-cost-throughput.md)
* Saiba mais sobre como [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre como [otimizar o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre como [otimizar o custo de consultas](optimize-cost-queries.md)
* Saiba mais sobre como [otimizar o custo de contas do Azure Cosmos de várias regiões](optimize-cost-regions.md)

