---
title: Otimizando o desenvolvimento e teste no Azure Cosmos DB
description: Este artigo explica como o Azure Cosmos DB oferece várias opções para desenvolvimento e teste do serviço gratuitamente.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: f9cb18b66def144b84de708351743832d1831fbf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65967275"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Otimizar o desenvolvimento e teste de custo no Azure Cosmos DB

Este artigo descreve as diferentes opções para utilizar o Azure Cosmos DB para desenvolvimento e teste gratuitamente do custo.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB emulator (versão localmente para download)

[Emulador do Azure Cosmos DB](local-emulator.md) é uma versão de transferência local que imita o serviço de nuvem do Azure Cosmos DB. Pode escrever e testar o código que utiliza as APIs do Azure Cosmos DB, mesmo que não tenha nenhuma ligação de rede e sem incorrer em custos. Emulador do Azure Cosmos DB fornece um ambiente local para fins de desenvolvimento com alta fidelidade para o serviço em nuvem. Pode desenvolver e testar a aplicação localmente, sem criar uma subscrição do Azure. Quando estiver pronto para implementar a sua aplicação para a cloud, atualize a cadeia de ligação para ligar ao ponto final do Azure Cosmos DB na cloud, não existem outras modificações que necessita. Também pode [configure um pipeline CI/CD com o emulador do Azure Cosmos DB](tutorial-setup-ci-cd.md) tarefa em DevOps do Azure para executar testes de compilação. Pode começar a utilizar ao visitar a [do Azure Cosmos DB emulator](local-emulator.md) artigo.

## <a name="try-azure-cosmos-db-for-free"></a>Experimentar o Azure Cosmos DB gratuitamente

[Experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) é gratuito de experiência de custos que permite-lhe criar base de dados e coleções e experimente com o Azure Cosmos DB na cloud. Não tem para se inscrever para o Azure ou pagar quaisquer custos. As contas de try Azure Cosmos DB estão disponíveis para um período limitado, atualmente, 30 dias. Pode renová-los em qualquer altura. Experimente o Azure Cosmos DB contas facilita a avaliar o Azure Cosmos DB, criar e testar uma aplicação com o inícios rápidos ou tutoriais. Pode criar uma demonstração ou efetuar o teste de unidade sem incorrer em custos. Ao utilizar Experimente o Azure Cosmos DB para contas gratuitas, lhe permitirá avaliar as capacidades de premium do Azure Cosmos DB gratuitamente, incluindo chave na mão global modelos de distribuição, os SLAs e consistência. Pode criar uma base de dados com um máximo de 25 contentores do Azure Cosmos e 10.000 RU/s de débito. Pode executar o aplicativo de exemplo sem subscrever uma conta do Azure ou com cartão de crédito. Experimente o Azure Cosmos DB gratuitamente, pode criar uma conta do Cosmos do Azure de várias regiões e executar uma aplicação no mesmo em apenas alguns minutos. Para começar a utilizar, veja [Experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) página.

## <a name="azure-free-account"></a>Conta Gratuita do Azure

O Azure Cosmos DB está incluído nos [conta gratuita do Azure](https://azure.microsoft.com/free), que oferece créditos do Azure e os recursos gratuitamente por um determinado período de tempo. Especificamente para o Azure Cosmos DB, nesta conta gratuita oferece 5 GB de armazenamento e 400 RUs do débito aprovisionado no ano inteiro. Esta experiência permite que qualquer programador testar os recursos do Azure Cosmos DB ou integrá-lo com outros serviços do Azure a custo zero facilmente. Conta gratuita do Azure, obtenha um crédito de 200 $ para gastar nos primeiros 30 dias. Não será cobrado, mesmo que começar a usar os serviços até escolher atualizar. Para começar, visite [conta gratuita do Azure](https://azure.microsoft.com/free) página.

## <a name="next-steps"></a>Passos Seguintes

Pode começar a utilizar com o emulador ou as contas do Azure Cosmos DB gratuitas com os seguintes artigos:

* Saiba mais sobre [otimizar para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre [entender a sua fatura do Azure Cosmos DB](understand-your-bill.md)
* Saiba mais sobre [otimizar o custo de débito](optimize-cost-throughput.md)
* Saiba mais sobre [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo de consultas](optimize-cost-queries.md)
* Saiba mais sobre [otimizar o custo de contas do Cosmos do Azure de várias regiões](optimize-cost-regions.md)

