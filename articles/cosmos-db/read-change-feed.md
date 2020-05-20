---
title: Leitura do feed de alterações do Azure Cosmos DB
description: Este artigo descreve diferentes opções disponíveis para ler e aceder à mudança de alimentos em Azure Cosmos DB.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 06e5a1a7b107f949dbb4945ef4d3116b9fa6d076
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83656595"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Leitura do feed de alterações do Azure Cosmos DB

Pode trabalhar com o feed de mudança De DB Azure Cosmos usando um modelo push ou um modelo de puxar. Com um modelo push, um cliente solicita trabalhar a partir de um servidor e tem lógica de negócio para processar uma mudança. No entanto, a complexidade na verificação de alterações e armazenamento de estado para as últimas alterações processadas é tratada no servidor.

Com um modelo de pull, um servidor solicita que funcione, solicitando-o frequentemente a partir de uma fila de trabalho central. O cliente, neste caso, não só tem lógica de negócio para processar alterações, mas também armazenar o Estado para a última alteração processada, manusear o equilíbrio de carga em vários clientes processando alterações paralelas e erros de manuseamento.

Ao ler a partir do feed de mudança DB Azure Cosmos, usar um modelo push é tipicamente recomendado porque você não precisa se preocupar com:

- Sondagem a mudança para futuras mudanças.
- Armazenar estado para a última mudança processada. Ao ler a partir do feed de mudança, este é automaticamente armazenado num recipiente de [aluguer](change-feed-processor.md#components-of-the-change-feed-processor).
- O equilíbrio de carga entre vários clientes que consomem alterações. Por exemplo, se um cliente não consegue acompanhar as alterações de processamento e outro tem capacidade disponível.
- [Erros de manuseamento](change-feed-processor.md#error-handling). Por exemplo, retentar automaticamente alterações falhadas que não foram processadas corretamente após uma exceção não tratada em código ou um problema de rede transitório.

A maioria dos cenários que usam o feed de mudança De DB Do Azure Cosmos usarão uma das opções do modelo push. No entanto, existem alguns cenários em que pode querer o controlo adicional de baixo nível do modelo de puxar. Incluem-se:

- Alterações de leitura de uma determinada chave de partição
- Controlar o ritmo a que o seu cliente recebe alterações para processamento
- Fazer uma leitura única dos dados existentes no feed de mudança (por exemplo, para fazer uma migração de dados)

## <a name="reading-change-feed-with-a-push-model"></a>Ler feed de mudança com um modelo push

Usar um modelo push é a maneira mais fácil de ler a partir do feed de mudança. Existem duas formas de ler a partir do feed de mudança com um modelo push: [Azure Functions Cosmos DB triggers](change-feed-functions.md) e a biblioteca de [processadores de feed](change-feed-processor.md)de mudança . A Azure Functions utiliza o processador de feed de mudança nos bastidores, pelo que ambas são formas muito semelhantes de ler o feed de mudança. Pense nas Funções Azure como simplesmente uma plataforma de hospedagem para o processador de feed de mudança, não uma forma totalmente diferente de ler o feed de mudança.

### <a name="azure-functions"></a>Funções do Azure

As Funções Azure são a opção mais simples se estiver apenas a começar a usar o feed de mudança. Devido à sua simplicidade, é também a opção recomendada para a maioria dos casos de utilização de alimentos para animais de alteração. Quando cria um gatilho de Funções Azure para o Azure Cosmos DB, seleciona o recipiente para se ligar e a Função Azure é acionada sempre que houver uma alteração no recipiente. Uma vez que a Azure Functions utiliza o processador de reparação de mudanças nos bastidores, paralelamente ao processamento de alterações nas [divisórias](partition-data.md)do seu recipiente .

Desenvolver-se com as Funções Azure é uma experiência fácil e pode ser mais rápido do que implementar o processador de feed de mudança por conta própria. Os gatilhos podem ser criados utilizando o portal Funções Azure ou programáticamente com SDKs. O Visual Studio e o VS Code fornecem suporte para escrever Funções Azure, e pode até utilizar as Funções Azure CLI para desenvolvimento de plataformas cruzadas. Pode escrever e depurar o código no seu ambiente de trabalho e, em seguida, implementar a função com um clique. Consulte a computação da base de [dados Serverless utilizando funções Azure](serverless-computing-database.md) e utilizando o feed de mudança com artigos de [Funções Azure](change-feed-functions.md) para saber mais.

### <a name="change-feed-processor-library"></a>Alterar biblioteca de processadores de feed

O processador de feed de mudança dá-lhe mais controlo do feed de mudança e ainda esconde a maior complexidade. A biblioteca do processador de feed de mudança segue o padrão de observador, onde a sua função de processamento é chamada pela biblioteca. A biblioteca do processador de feed de mudança verificará automaticamente se há alterações e, se forem encontradas alterações, "empurre" para o cliente. Se tiver um feed de mudança de alta alimentação, pode instantaneamente vários clientes lerem o feed de mudança. A biblioteca de processadores de feed de mudança dividirá automaticamente a carga entre os diferentes clientes. Não terá de implementar nenhuma lógica para equilibrar a carga entre vários clientes ou qualquer lógica para manter o estado de arrendamento.

A biblioteca de processadores de feed de mudança garante uma entrega "pelo menos uma vez" de todas as alterações. Por outras palavras, se utilizar a biblioteca do processador de feed de mudanças, a sua função de processamento será chamada com sucesso para cada item do feed de mudança. Se houver uma exceção não tratada na lógica do negócio na sua função de processamento, as alterações falhadas serão novamente tentadas até que sejam processadas com sucesso. Para evitar que o seu processador de feed de mudança fique "preso" continuamente a tentar as mesmas alterações, adicione lógica na sua função de processamento para escrever documentos, exceto, a uma fila de letras mortas. Saiba mais sobre [o manuseamento de erros.](change-feed-processor.md#error-handling)

Nas Funções Azure, a recomendação para lidar com erros é a mesma. Você ainda deve adicionar lógica no seu código delegado para escrever documentos, exceção, a uma fila de cartas mortas. No entanto, se houver uma exceção não tratada na sua Função Azure, a mudança que gerou a exceção não será automaticamente tentada. Se houver uma exceção não tratada na lógica do negócio, a Função Azure passará a processar a próxima alteração. A Função Azure não voltará a tentar a mesma mudança falhada.

Tal como o Azure Functions, desenvolver-se com a biblioteca de processadores de feed de mudança é fácil. No entanto, é responsável pela implementação de um ou mais anfitriões para o processador de feed de mudança. Um hospedeiro é uma instância de aplicação que utiliza o processador de feed de mudança para ouvir alterações. Enquanto as Funções Azure têm capacidades para escalaautomática, é responsável pela escala automática dos seus anfitriões. Para saber mais, consulte [a utilização do processador](change-feed-processor.md#dynamic-scaling)de feed de mudança . A biblioteca de processadores de feed de mudança faz parte do [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3).

## <a name="reading-change-feed-with-a-pull-model"></a>Ler feed de mudança com um modelo de puxar

O modelo de puxar por [alterações](change-feed-pull-model.md) permite-lhe consumir o feed de mudança ao seu próprio ritmo. As alterações devem ser solicitadas pelo cliente e não há sondagens automáticas para alterações. Se quiser "marcar" permanentemente a última alteração processada (semelhante ao recipiente de aluguer do modelo push), terá de guardar um sinal de [continuação](change-feed-pull-model.md#saving-continuation-tokens).

Utilizando o modelo de puxar por alterações, obtém-se um controlo mais baixo do feed de mudança. Ao ler o feed de mudança com o modelo pull, tem três opções:

- Leia alterações para um recipiente inteiro
- Ler alterações para um [FeedRange](change-feed-pull-model.md#using-feedrange-for-parallelization) específico
- Ler alterações para um valor-chave de divisória específico

Pode paralelamente ao processamento de alterações em vários clientes, tal como pode com o processador de feed de mudança. No entanto, o modelo de pull não lida automaticamente com o equilíbrio de carga entre os clientes. Quando utilizar o modelo de pull para parallelizar o processamento do feed de mudança, obterá primeiro uma lista de FeedRanges. Um FeedRange abrange uma gama de valores-chave de divisória. Terá de ter um processo orquestrador que obtenha FeedRanges e os distribua entre as suas máquinas. Em seguida, pode utilizar estes FeedRanges para que várias máquinas leiam o feed de mudança em paralelo.

Não existe uma garantia de entrega "pelo menos uma vez" incorporada com o modelo pull. O modelo de puxar dá-lhe um controlo de baixo nível para decidir como gostaria de lidar com erros.

> [!NOTE]
> O modelo de pull de feed de mudança está atualmente em [pré-visualização apenas no Azure Cosmos DB .NET SDK.](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview) A pré-visualização ainda não está disponível para outras versões SDK.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Alterar feed em APIs para Cassandra e MongoDB

A funcionalidade de alimentação de mudanças é surgida à medida que os fluxos de mudança em MongoDB API e Query com predicado em Cassandra API. Para saber mais sobre os detalhes de implementação da MongoDB API, consulte os [streams Change no Azure Cosmos DB API para MongoDB](mongodb-change-streams.md).

A Nativa Apache Cassandra fornece a captura de dados de mudança (CDC), um mecanismo para sinalizar tabelas específicas para o arquivo, bem como rejeitar escritos para essas tabelas uma vez que um tamanho configurável no disco para o log CDC é alcançado. A funcionalidade de feed de mudança em Azure Cosmos DB API para Cassandra aumenta a capacidade de consultar as mudanças com predicado via CQL. Para saber mais sobre os detalhes da implementação, consulte [change feed no Azure Cosmos DB API para Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Passos seguintes

Pode agora continuar a aprender mais sobre a mudança de alimentos nos seguintes artigos:

* [Visão geral do feed de mudança](change-feed.md)
* [Utilização de feed de mudança com funções azure](change-feed-functions.md)
* [Usando a biblioteca de processadores de feed de mudança](change-feed-processor.md)
