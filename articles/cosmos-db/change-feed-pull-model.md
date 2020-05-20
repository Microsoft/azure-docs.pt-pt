---
title: Modelo Pull do feed de alterações
description: Saiba como usar o modelo de pull de feed de mudança Azure Cosmos DB para ler o feed de mudança e as diferenças entre o modelo pull e o Processador change feed
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/19/2020
ms.reviewer: sngun
ms.openlocfilehash: c47016d0b82a4e4ed084f5d82394d91fd2b46be1
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697723"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Alterar modelo de puxar por feed em Azure Cosmos DB

Com o modelo de pull de feed de mudança, você pode consumir o feed de mudança De DB Azure Cosmos ao seu próprio ritmo. Como já pode fazer com o processador de feed de [mudança,](change-feed-processor.md)pode utilizar o modelo de relojoamento de reparação para paralelor o processamento de alterações entre os consumidores de feed de múltiplas alterações.

> [!NOTE]
> O modelo de pull de feed de mudança está atualmente em [pré-visualização apenas no Azure Cosmos DB .NET SDK.](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview) A pré-visualização ainda não está disponível para outras versões SDK.

## <a name="consuming-an-entire-containers-changes"></a>Consumir as alterações de um recipiente inteiro

Pode criar um para processar o feed de `FeedIterator` mudança utilizando o modelo pull. Quando inicialmente cria `FeedIterator` um, pode especificar um opcional `StartTime` dentro do `ChangeFeedRequestOptions` . Quando não especificado, será `StartTime` a hora atual.

O `FeedIterator` vem em dois sabores. Além dos exemplos abaixo que devolvem objetos de entidade, também pode obter a resposta com `Stream` suporte. Os streams permitem-lhe ler dados sem os desserializar primeiro, poupando nos recursos dos clientes.

Aqui está um exemplo para obter um `FeedIterator` que devolve objetos de entidade, neste caso um `User` objeto:

```csharp
FeedIterator<User> iteratorWithPOCOS = container.GetChangeFeedIterator<User>();
```

Aqui está um exemplo para obter um `FeedIterator` que devolve `Stream` um:

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator();
```

Utilizando um `FeedIterator` , pode facilmente processar a alimentação de mudança de um recipiente inteiro ao seu próprio ritmo. Eis um exemplo:

```csharp
FeedIterator<User> iteratorForTheEntireContainer= container.GetChangeFeedIterator<User>();

while (iteratorForTheEntireContainer.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="consuming-a-partition-keys-changes"></a>Consumir as alterações da chave de partição

Em alguns casos, só pode querer processar as alterações específicas da chave de partição. Você pode obter um `FeedIterator` para uma chave de partição específica e processar as alterações da mesma forma que você pode para um recipiente inteiro.

```csharp
FeedIterator<User> iteratorForThePartitionKey = container.GetChangeFeedIterator<User>(new PartitionKey("myPartitionKeyValueToRead"));

while (iteratorForThePartitionKey.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>Utilização do FeedRange para paratetização

No [processador de feed de mudança,](change-feed-processor.md)o trabalho é automaticamente distribuído por vários consumidores. No modelo de puxar por alterações, pode utilizar o modelo `FeedRange` para paralelor o processamento do feed de alteração. A `FeedRange` representa uma gama de valores-chave de divisória.

Aqui está um exemplo que mostra como obter uma lista de gamas para o seu recipiente:

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

Quando obtiver a lista de FeedRanges para o seu recipiente, obtém um `FeedRange` por [partição física](partition-data.md#physical-partitions).

Utilizando `FeedRange` um, pode então criar um `FeedIterator` para parallelizar o processamento do feed de mudança em várias máquinas ou fios. Ao contrário do exemplo anterior que mostrou como obter um único `FeedIterator` para todo o recipiente, pode utilizar o `FeedRange` para obter vários FeedIterators que podem processar a alimentação de mudança em paralelo.

No caso de pretender utilizar feedRanges, é necessário ter um processo de orquestração que obtenha FeedRanges e os distribua para essas máquinas. Esta distribuição pode ser:

* Usando e distribuindo este valor de `FeedRange.ToJsonString` cadeia. Os consumidores podem usar este valor com`FeedRange.FromJsonString`
* Se a distribuição estiver em processo, passe a referência do `FeedRange` objeto.

Aqui está uma amostra que mostra como ler desde o início do alimento de mudança do recipiente usando duas hipotéticas máquinas separadas que estão lendo em paralelo:

Máquina 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<User>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorA.HasMoreResults)
{
   FeedResponse<User> users = await iteratorA.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

Máquina 2:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ranges[1], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorB.HasMoreResults)
{
   FeedResponse<User> users = await iteratorB.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="saving-continuation-tokens"></a>Fichas de continuação de poupança

Pode salvar a sua posição criando um símbolo de `FeedIterator` continuação. Um símbolo de continuação é um valor de cadeia que mantém o registo das últimas alterações processadas do seu FeedIterator. Isto permite que o `FeedIterator` retomar neste momento mais tarde. O seguinte código será lido através da alimentação de mudança desde a criação do contentor. Depois de não existirem mais alterações, persistirá um sinal de continuação para que o consumo de alimentos para animais possa ser retomado mais tarde.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

string continuation = null;

while (iterator.HasMoreResults)
{
   FeedResponse<User> users = await iterator.ReadNextAsync();
   continuation = users.ContinuationToken;

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(continuation);
```

Enquanto o contentor cosmos ainda existir, o sinal de continuação de um alimentador nunca expira.

## <a name="comparing-with-change-feed-processor"></a>Comparar com o processador de feed de mudança

Muitos cenários podem processar o feed de mudança usando o processador de feed de [mudança](change-feed-processor.md) ou o modelo pull. As fichas de continuação do modelo pull e o recipiente de aluguer do processador de reprodução de alterações são ambos "marcadores" para o último item processado (ou lote de itens) no feed de mudança.
No entanto, não pode converter fichas de continuação num contentor de aluguer (ou vice-versa).

Deve considerar a utilização do modelo de puxar nestes cenários:

- Alterações de leitura de uma determinada chave de partição
- Controlar o ritmo a que o seu cliente recebe alterações para processamento
- Fazer uma leitura única dos dados existentes no feed de mudança (por exemplo, para fazer uma migração de dados)

Aqui estão algumas diferenças fundamentais entre o processador de feed de mudança e o modelo pull:

|  | Processador do feed de alterações| Modelo de puxar |
| --- | --- | --- |
| Acompanhar o ponto atual no processamento de alimentos para alterações | Arrendamento (armazenado num contentor Azure Cosmos DB) | Ficha de continuação (armazenada na memória ou persistiu manualmente) |
| Capacidade de reproduzir alterações passadas | Sim, com o modelo push | Sim, com modelo de puxar|
| Sondagens para futuras mudanças | Verifica automaticamente as alterações baseadas no utilizador especificado`WithPollInterval` | Manual |
| Alterações de processo de todo o recipiente | Sim, e automaticamente paralelizado através de vários fios/máquina consumindo do mesmo recipiente| Sim, e manualmente paralelizado usando FeedTokens |
| Alterações de processo a partir de apenas uma chave de partição | Não suportado | Sim|
| Nível de apoio | Disponível em Geral | Pré-visualizar |

## <a name="next-steps"></a>Passos seguintes

* [Visão geral do feed de mudança](change-feed.md)
* [Utilização do processador de feed de mudança](change-feed-processor.md)
* [Acionar Funções do Azure](change-feed-functions.md)