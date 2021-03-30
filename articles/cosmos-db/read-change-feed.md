---
title: Leitura do feed de alterações do Azure Cosmos DB
description: Este artigo descreve diferentes opções disponíveis para ler e aceder ao feed de mudança em Azure Cosmos DB.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/27/2020
ms.reviewer: sngun
ms.openlocfilehash: 7021367e1230573343ddf57ccd399d998ad5280e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339279"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Leitura do feed de alterações do Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Pode trabalhar com o feed de mudança de DB Azure Cosmos utilizando um modelo de push ou um modelo de puxar. Com um modelo push, o processador de feed de mudança empurra o trabalho para um cliente que tem lógica de negócio para processar este trabalho. No entanto, a complexidade na verificação do trabalho e do estado de armazenamento do último trabalho processado é tratada dentro do processador de feed de alteração.

Com um modelo de puxar, o cliente tem que retirar o trabalho do servidor. O cliente, neste caso, não só tem lógica de negócio para o processamento de trabalho, mas também armazena o estado para o último trabalho processado, manuseando o equilíbrio de cargas entre vários clientes processando trabalhos em paralelo, e manipulando erros.

Ao ler o feed de mudança de DB do Azure Cosmos, recomendamos a utilização de um modelo de push porque não precisa de se preocupar com:

- A sondar a mudança para futuras mudanças.
- Estado de armazenamento para a última mudança processada. Ao ler a partir do feed de alteração, este é automaticamente armazenado num [recipiente de locação](change-feed-processor.md#components-of-the-change-feed-processor).
- Carregamento equilibrando vários clientes consumindo mudanças. Por exemplo, se um cliente não consegue acompanhar as alterações de processamento e outro tem capacidade disponível.
- [Erros de manuseamento](change-feed-processor.md#error-handling). Por exemplo, rectição automática de alterações falhadas que não foram processadas corretamente após uma exceção não manipulada no código ou um problema de rede transitório.

A maioria dos cenários que usam o feed de mudança de DB Azure Cosmos usará uma das opções do modelo push. No entanto, existem alguns cenários em que poderá querer o controlo adicional de baixo nível do modelo de puxar. Incluem-se:

- Alterações de leitura a partir de uma chave de partição particular
- Controlando o ritmo em que o seu cliente recebe alterações para o processamento
- Fazer uma leitura única dos dados existentes no feed de alteração (por exemplo, para fazer uma migração de dados)

## <a name="reading-change-feed-with-a-push-model"></a>Ler mudar o feed com um modelo de push

A utilização de um modelo de push é a forma mais fácil de ler a partir do feed de alteração. Existem duas formas de ler a partir do feed de alteração com um modelo push: [Azure Functions Cosmos DB triggers](change-feed-functions.md) e a biblioteca do [processador de feed de alteração](change-feed-processor.md). O Azure Functions utiliza o processador de feed de mudança nos bastidores, pelo que estas são ambas formas muito semelhantes de ler o feed de mudança. Pense nas Funções Azure como simplesmente uma plataforma de hospedagem para o processador de feed de mudança, não uma forma totalmente diferente de ler o feed de mudança.

### <a name="azure-functions"></a>Funções do Azure

As Funções Azure são a opção mais simples se estiver apenas a começar a utilizar o feed de alteração. Devido à sua simplicidade, é também a opção recomendada para a maioria dos casos de utilização de alimentos para animais de alteração. Quando cria um gatilho de Funções Azure para Azure Cosmos DB, seleciona o recipiente para ligar e a Função Azure é ativada sempre que há uma alteração no recipiente. Uma vez que o Azure Functions utiliza o processador de feed de alteração nos bastidores, ele páraqueliza automaticamente o processamento de mudança através das [divisórias](partitioning-overview.md)do seu recipiente .

Desenvolver-se com a Azure Functions é uma experiência fácil e pode ser mais rápido do que implementar o processador de change feed por si próprio. Os gatilhos podem ser criados utilizando o portal Azure Functions ou utilizando programáticamente SDKs. Visual Studio e VS Code fornecem suporte para escrever Funções Azure, e você pode até usar o CLI funções Azure para o desenvolvimento de plataformas cruzadas. Pode escrever e desordiá-lo no seu ambiente de trabalho e, em seguida, implementar a função com um clique. Consulte a computação da [base de dados serverless utilizando funções Azure](serverless-computing-database.md) e [usando o feed de alteração com artigos de Funções Azure](change-feed-functions.md) para saber mais.

### <a name="change-feed-processor-library"></a>Alterar biblioteca de processadores de feed

O processador de feed de alteração dá-lhe mais controlo do feed de mudança e ainda esconde a maior complexidade. A biblioteca do processador de feed de alteração segue o padrão de observador, onde a sua função de processamento é chamada pela biblioteca. A biblioteca do processador de feed de alteração verificará automaticamente as alterações e, se forem encontradas alterações, "empurre" para o cliente. Se tiver um feed de mudança de produção elevado, pode instantaneamente várias clientes para ler o feed de mudança. A biblioteca do processador de feed de alteração dividirá automaticamente a carga entre os diferentes clientes. Não terá de implementar nenhuma lógica de equilíbrio de carga entre vários clientes ou qualquer lógica para manter o estado de arrendamento.

A biblioteca do processador de feed de alteração garante uma entrega "pelo menos uma vez" de todas as alterações. Por outras palavras, se utilizar a biblioteca do processador de feed change, a sua função de processamento será chamada com sucesso para cada item no feed de alteração. Se houver uma exceção não manipulada na lógica de negócio na sua função de processamento, as alterações falhadas serão novamente experimentadas até que sejam processadas com sucesso. Para evitar que o seu processador de feed de alteração fique "preso" continuamente a tentar as mesmas alterações, adicione lógica na sua função de processamento para escrever documentos, com exceção, a uma fila de letras mortas. Saiba mais sobre [o manuseamento de erros.](change-feed-processor.md#error-handling)

Nas Funções Azure, a recomendação para o manuseamento de erros é a mesma. Deve ainda adicionar lógica no seu código de delegado para escrever documentos, por exceção, a uma fila de letras mortas. No entanto, se houver uma exceção não manipulada na sua Função Azure, a alteração que gerou a exceção não será automaticamente novamente julgada. Se houver uma exceção não manipulada na lógica de negócio, a Função Azure passará a processar a próxima alteração. A Função Azure não voltará a tentar a mesma alteração falhada.

Tal como as Funções Azure, desenvolver-se com a biblioteca do processador de change feed é fácil. No entanto, é responsável pela implementação de um ou mais anfitriões para o processador change feed. Um anfitrião é uma instância de aplicação que usa o processador de feed de alteração para ouvir alterações. Enquanto as Funções Azure têm capacidades para dimensionamento automático, é responsável por escalar os seus anfitriões. Para saber mais, consulte [utilizando o processador change feed](change-feed-processor.md#dynamic-scaling). A biblioteca de processadores de feed de alteração faz parte do [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3).

## <a name="reading-change-feed-with-a-pull-model"></a>Ler mudar o feed com um modelo de puxar

O [modelo de puxar o feed de mudança](change-feed-pull-model.md) permite-lhe consumir o feed de mudança ao seu próprio ritmo. As alterações devem ser solicitadas pelo cliente e não há sondagens automáticas para alterações. Se quiser "reservar" permanentemente a última alteração processada (semelhante ao recipiente de locação do modelo push), terá de [guardar um token de continuação](change-feed-pull-model.md#saving-continuation-tokens).

Utilizando o modelo de puxar o feed de mudança, obtém-se um controlo de baixo nível do feed de mudança. Ao ler o feed de mudança com o modelo pull, tem três opções:

- Ler alterações para um recipiente inteiro
- Ler alterações para um [FeedRange](change-feed-pull-model.md#using-feedrange-for-parallelization) específico
- Leia alterações para um valor chave de partição específico

Pode paralelizar o processamento de alterações em vários clientes, tal como pode com o processador change feed. No entanto, o modelo pull não lida automaticamente com o equilíbrio de carga entre os clientes. Quando utilizar o modelo pull para paralelizar o processamento do feed de alteração, obterá primeiro uma lista de FeedRanges. Um FeedRange abrange uma gama de valores-chave de partição. Terá de ter um processo de orquestrador que obtenha feedRanges e os distribua entre as suas máquinas. Em seguida, pode utilizar estes FeedRanges para que várias máquinas leiam o feed de mudança em paralelo.

Não existe garantia de entrega incorporada "pelo menos uma vez" com o modelo pull. O modelo de puxar dá-lhe um controlo de baixo nível para decidir como gostaria de lidar com erros.

> [!NOTE]
> O modelo de pull de feed de mudança está atualmente [em pré-visualização apenas no Azure Cosmos DB .NET SDK.](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.15.0-preview) A pré-visualização ainda não está disponível para outras versões SDK.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Alterar feed em APIs para Cassandra e MongoDB

A funcionalidade de feed de alteração é emergida como fluxos de mudança na API mongoDB e consulta com predicado em Cassandra API. Para saber mais sobre os detalhes de implementação da MongoDB API, consulte os [streams Change no AZure Cosmos DB API para a MongoDB](mongodb-change-streams.md).

A Nativa Apache Cassandra fornece a captura de dados de mudança (CDC), um mecanismo para sinalizar tabelas específicas para arquivo, bem como rejeitar escritas para essas tabelas uma vez que um tamanho-on-disco configurável para o log CDC é alcançado. A funcionalidade de feed de mudança em Azure Cosmos DB API para Cassandra aumenta a capacidade de consultar as mudanças com predicado via CQL. Para saber mais sobre os detalhes da implementação, consulte [change feed na API API do Azure Cosmos para Cassandra.](cassandra-change-feed.md)

## <a name="next-steps"></a>Passos seguintes

Pode agora continuar a aprender mais sobre a alteração do feed nos seguintes artigos:

* [Visão geral do feed de mudança](change-feed.md)
* [Utilização de feed de alteração com Funções Azure](change-feed-functions.md)
* [Utilização da biblioteca de processadores de feed de alteração](change-feed-processor.md)
