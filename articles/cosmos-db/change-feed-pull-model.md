---
title: Modelo Pull do feed de alterações
description: Saiba como utilizar o modelo de puxar o feed de alteração Azure Cosmos para ler o feed de alteração e as diferenças entre o modelo de puxar e alterar o processador De feed
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/04/2021
ms.reviewer: sngun
ms.openlocfilehash: e227e230c4de1234e068f72958367dc2ac709426
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97881978"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Alterar modelo de puxar feed em Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Com o modelo de pull de feed de mudança, você pode consumir o Azure Cosmos DB mudar de feed ao seu próprio ritmo. Como já pode fazer com o [processador change feed](change-feed-processor.md), pode utilizar o modelo de pull de feed de alteração para paralelizar o processamento de alterações em vários consumidores de feed de alterações múltiplas.

> [!NOTE]
> O modelo de pull de feed de mudança está atualmente [em pré-visualização apenas no Azure Cosmos DB .NET SDK.](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.15.0-preview) A pré-visualização ainda não está disponível para outras versões SDK.

## <a name="comparing-with-change-feed-processor"></a>Comparando com o processador change feed

Muitos cenários podem processar o feed de alteração usando o [processador de alimentação de alteração](change-feed-processor.md) ou o modelo de puxar. Os tokens de continuação do modelo pull e o recipiente de locação do processador de alteração são ambos "marcadores" para o último item processado (ou lote de itens) no feed de alteração.

No entanto, não é possível converter fichas de continuação num recipiente de arrendamento (ou vice-versa).

> [!NOTE]
> Na maioria dos casos, quando é necessário ler a partir do feed de alteração, a opção mais simples é utilizar o [processador de alimentação de alteração](change-feed-processor.md).

Deve considerar a utilização do modelo de puxar nestes cenários:

- Leia as alterações a partir de uma chave de partição particular
- Controle o ritmo a que o seu cliente recebe alterações para o processamento
- Efetue uma leitura única dos dados existentes no feed de alteração (por exemplo, para fazer uma migração de dados)

Aqui estão algumas diferenças fundamentais entre o processador de alimentação de alteração e o modelo de puxar:

|Funcionalidade  | Processador do feed de alterações| Modelo de puxar |
| --- | --- | --- |
| Acompanhar o ponto atual no processamento do feed de alteração | Locação (armazenada num recipiente DB Azure Cosmos) | Ficha de continuação (armazenada na memória ou persistiu manualmente) |
| Capacidade de reproduzir alterações passadas | Sim, com o modelo push | Sim, com o modelo pull|
| Sondagem para futuras mudanças | Verifica automaticamente as alterações com base nas especificadas do utilizador `WithPollInterval` | Manual |
| Comportamento onde não há novas alterações | Aguardar `WithPollInterval` e rever verificação automática | Deve pegar a exceção e verificar manualmente |
| Mudanças de processo a partir de todo o contentor | Sim, e automaticamente paralizada através de múltiplos fios/máquina consumindo a partir do mesmo recipiente| Sim, e manualmente paralelo usando FeedTokens |
| O processo muda a partir de apenas uma chave de partição | Não suportado | Yes|
| Nível de suporte | Disponível em Geral | Pré-visualizar |

> [!NOTE]
> Ao contrário de ler o processador change feed, deve lidar explicitamente com os casos em que não há novas alterações. 

## <a name="consuming-an-entire-containers-changes"></a>Consumir as mudanças de um contentor inteiro

Pode criar um `FeedIterator` para processar o feed de alteração utilizando o modelo pull. Quando inicialmente criar um `FeedIterator` , deve especificar um valor necessário `ChangeFeedStartFrom` que consiste tanto na posição inicial para as alterações de leitura como na desejada `FeedRange` . `FeedRange`Trata-se de uma gama de valores-chave de partição e especifica os itens que serão lidos a partir do feed de alteração utilizando esse específico `FeedIterator` .

Pode especificar opcionalmente `ChangeFeedRequestOptions` para definir um `PageSizeHint` . É `PageSizeHint` o número máximo de itens que serão devolvidos numa única página.

O `FeedIterator` vem em dois sabores. Além dos exemplos abaixo que a entidade de retorno objetos, você também pode obter a resposta com `Stream` suporte. Os streams permitem-lhe ler dados sem os ter primeiro desercializado, poupando nos recursos dos clientes.

Aqui está um exemplo para obter um `FeedIterator` que devolve objetos de entidade, neste caso um `User` objeto:

```csharp
FeedIterator<User> InteratorWithPOCOS = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning());
```

Aqui está um exemplo para obter um `FeedIterator` que devolve `Stream` a:

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Beginning());
```

Se não fornecer um `FeedRange` a , pode processar a rede de mudanças de um recipiente inteiro ao seu próprio `FeedIterator` ritmo. Aqui está um exemplo que começa a ler todas as mudanças a partir do momento atual:

```csharp
FeedIterator iteratorForTheEntireContainer = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Now());

while (iteratorForTheEntireContainer.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

Como o feed de mudança é efetivamente uma lista infinita de itens que englobam todas as futuras escritas e atualizações, o valor de `HasMoreResults` é sempre verdadeiro. Quando tentar ler o feed de mudança e não houver novas alterações disponíveis, receberá uma exceção. No exemplo acima, a exceção é tratada esperando 5 segundos antes de voltar a verificar as alterações.

## <a name="consuming-a-partition-keys-changes"></a>Consumir as mudanças de uma chave de partição

Em alguns casos, pode apenas querer processar as alterações de uma chave de partição específica. Pode obter uma `FeedIterator` chave de partição específica e processar as alterações da mesma forma que pode para um recipiente inteiro.

```csharp
FeedIterator<User> iteratorForPartitionKey = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(FeedRange.FromPartitionKey(new PartitionKey("PartitionKeyValue"))));

while (iteratorForThePartitionKey.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>Utilização do FeedRange para paralelização

No [processador change feed](change-feed-processor.md), o trabalho é automaticamente distribuído por vários consumidores. No modelo de puxar o feed de alteração, pode utilizar `FeedRange` o paralelismo para paralelizar o processamento do feed de alteração. A `FeedRange` representa uma gama de valores-chave de partição.

Aqui está um exemplo que mostra como obter uma lista de gamas para o seu recipiente:

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

Quando obtiver a lista de FeedRanges para o seu recipiente, terá uma `FeedRange` por [partição física.](partitioning-overview.md#physical-partitions)

Utilizando um `FeedRange` , pode então criar um `FeedIterator` paralelizar o processamento da rede de mudanças através de várias máquinas ou fios. Ao contrário do exemplo anterior que mostrou como obter um `FeedIterator` para todo o recipiente ou uma única tecla de partição, pode utilizar o FeedRanges para obter vários FeedIterators que podem processar o feed de mudança em paralelo.

No caso de querer utilizar o FeedRanges, é necessário ter um processo de orquestrador que obtenha feedRanges e os distribua para essas máquinas. Esta distribuição pode ser:

* Usando `FeedRange.ToJsonString` e distribuindo este valor de cadeia. Os consumidores podem usar este valor com `FeedRange.FromJsonString`
* Se a distribuição estiver em curso, passando a referência do `FeedRange` objeto.

Aqui está uma amostra que mostra como ler desde o início da alimentação de mudança do recipiente usando duas máquinas hipotéticas separadas que estão lendo em paralelo:

Máquina 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[0]));
while (iteratorA.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorA.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

Máquina 2:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[1]));
while (iteratorB.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorA.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

## <a name="saving-continuation-tokens"></a>Salvando fichas de continuação

Pode guardar a posição do seu `FeedIterator` criando um símbolo de continuação. Um token de continuação é um valor de cadeia que mantém o registo das últimas alterações processadas do feedIterator. Isto permite `FeedIterator` que o retomar neste ponto mais tarde. O seguinte código será lido através do feed de alteração desde a criação do recipiente. Depois de não haver mais alterações disponíveis, persistirá uma continuação do consumo de alimentos para que o consumo de alimentos para animais possa ser retomado mais tarde.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning());

string continuation = null;

while (iterator.HasMoreResults)
{
   try { 
        FeedResponse<User> users = await iterator.ReadNextAsync();
        continuation = users.ContinuationToken;

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
   }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }   
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.ContinuationToken(continuation));
```

Enquanto o contentor cosmos ainda existir, o símbolo de continuação de um FeedIterator nunca expira.

## <a name="next-steps"></a>Passos seguintes

* [Visão geral do feed de mudança](change-feed.md)
* [Utilização do processador change feed](change-feed-processor.md)
* [Acionar Funções do Azure](change-feed-functions.md)