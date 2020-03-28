---
title: 'Exemplo: Adicionar rostos a um PersonGroup - Face'
titleSuffix: Azure Cognitive Services
description: Este guia demonstra como adicionar um grande número de pessoas e rostos a um objeto do PersonGroup com o serviço Azure Cognitive Services Face.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 240905d538afc5c0f4b7f0e0bf400fac23c3183f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169834"
---
# <a name="add-faces-to-a-persongroup"></a>Adicionar rostos a um PersonGroup

Este guia demonstra como adicionar um grande número de pessoas e rostos a um objeto PersonGroup. A mesma estratégia também se aplica aos objetos LargePersonGroup, FaceList e LargeFaceList. Esta amostra é escrita em C# utilizando a biblioteca de clientes Azure Cognitive Services Face .NET.

## <a name="step-1-initialization"></a>Passo 1: inicialização

O seguinte código declara várias variáveis e implementa uma função de ajudante para agendar os pedidos de adição facial:

- `PersonCount` é o número total de pessoas.
- `CallLimitPerSecond` é o número máximo de chamadas por segundo de acordo com o escalão de subscrição.
- `_timeStampQueue` é uma Fila para registrar os carimbo de data/hora dos pedidos.
- `await WaitCallLimitPerSecondAsync()`aguarda até que seja válido enviar o próximo pedido.

```csharp
const int PersonCount = 10000;
const int CallLimitPerSecond = 10;
static Queue<DateTime> _timeStampQueue = new Queue<DateTime>(CallLimitPerSecond);

static async Task WaitCallLimitPerSecondAsync()
{
    Monitor.Enter(_timeStampQueue);
    try
    {
        if (_timeStampQueue.Count >= CallLimitPerSecond)
        {
            TimeSpan timeInterval = DateTime.UtcNow - _timeStampQueue.Peek();
            if (timeInterval < TimeSpan.FromSeconds(1))
            {
                await Task.Delay(TimeSpan.FromSeconds(1) - timeInterval);
            }
            _timeStampQueue.Dequeue();
        }
        _timeStampQueue.Enqueue(DateTime.UtcNow);
    }
    finally
    {
        Monitor.Exit(_timeStampQueue);
    }
}
```

## <a name="step-2-authorize-the-api-call"></a>Passo 2: autorizar a chamada da API

Quando utiliza uma biblioteca de clientes, tem de passar a chave de subscrição do construtor da classe **FaceClient.** Por exemplo:

```csharp
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<SubscriptionKey>"),
    new System.Net.Http.DelegatingHandler[] { });
```

Para obter a chave de subscrição, vá ao Azure Marketplace a partir do portal Azure. Para mais informações, consulte [Subscrições](https://www.microsoft.com/cognitive-services/sign-up).

## <a name="step-3-create-the-persongroup"></a>Passo 3: Criar o PersonGroup

É criado um PersonGroup com o nome "MyPersonGroup" para guardar as pessoas.
A hora do pedido é colocada em fila em `_timeStampQueue` para garantir a validação geral.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceClient.LargePersonGroup.CreateAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>Passo 4: Criar as pessoas para o PersonGroup

As pessoas são criadas `await WaitCallLimitPerSecondAsync()` simultaneamente, e também são aplicadas para evitar exceder o limite de chamada.

```csharp
Person[] persons = new Person[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceClient.PersonGroupPerson.CreateAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>Passo 5: adicionar rostos às pessoas

Os rostos adicionados a diferentes pessoas são processados simultaneamente. Os rostos adicionados para uma pessoa específica são processados sequencialmente.
Mais `await WaitCallLimitPerSecondAsync()` uma vez, é invocado para garantir que a frequência de pedido está dentro do âmbito de limitação.

```csharp
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a>Resumo

Neste guia, aprendeu o processo de criação de um PersonGroup com um número maciço de pessoas e rostos. Alguns lembretes:

- Esta estratégia aplica-se também aos FaceLists e Grupos De Grandes Pessoas.
- A adição ou a pagando rostos a diferentes FaceLists ou pessoas em Grupos De Grandes Pessoas são processados simultaneamente.
- Adicionar ou apagar rostos a um FaceList específico ou pessoa num Grupo De PessoaS Maiores são feitos sequencialmente.
- Para a simplicidade, como lidar com uma potencial exceção é omitida neste guia. Se quiser aumentar a robustez, aplique a política de retry adequada.

As seguintes características foram explicadas e demonstradas:

- Criar PersonGroups utilizando o [PersonGroup - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API.
- Criar pessoas utilizando a [Pessoa do Grupo De Pessoa - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API.
- Adicione rostos a pessoas utilizando a Pessoa Do Grupo De Pessoa - Adicione a API [facial.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)

## <a name="related-topics"></a>Tópicos relacionados

- [Identificar rostos numa imagem](HowtoIdentifyFacesinImage.md)
- [Detetar rostos numa imagem](HowtoDetectFacesinImage.md)
- [Utilizar a funcionalidade de grande escala](how-to-use-large-scale.md)
