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
ms.custom: devx-track-csharp
ms.openlocfilehash: 3643fad1c9e821a78df6d78edeede2341ec79ea8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91303766"
---
# <a name="add-faces-to-a-persongroup"></a>Adicionar rostos a um PersonGroup

Este guia demonstra como adicionar um grande número de pessoas e rostos a um objeto do PersonGroup. A mesma estratégia também se aplica a objetos LargePersonGroup, FaceList e LargeFaceList. Esta amostra está escrita em C# utilizando a biblioteca de clientes Azure Cognitive Services Face .NET.

## <a name="step-1-initialization"></a>Passo 1: inicialização

O seguinte código declara várias variáveis e implementa uma função de ajudante para agendar os pedidos de adição facial:

- `PersonCount` é o número total de pessoas.
- `CallLimitPerSecond` é o número máximo de chamadas por segundo de acordo com o escalão de subscrição.
- `_timeStampQueue` é uma Fila para registrar os carimbo de data/hora dos pedidos.
- `await WaitCallLimitPerSecondAsync()` espera até que seja válido para enviar o próximo pedido.

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

Quando utilizar uma biblioteca de clientes, tem de passar a chave de subscrição para o construtor da classe **FaceClient.** Por exemplo:

```csharp
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<SubscriptionKey>"),
    new System.Net.Http.DelegatingHandler[] { });
```

Para obter a chave de subscrição, vá ao Azure Marketplace a partir do portal Azure. Para mais informações, consulte [Subscrições.](https://www.microsoft.com/cognitive-services/sign-up)

## <a name="step-3-create-the-persongroup"></a>Passo 3: Criar o PersonGroup

É criado um PersonGroup com o nome "MyPersonGroup" para guardar as pessoas.
A hora do pedido é colocada em fila em `_timeStampQueue` para garantir a validação geral.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceClient.LargePersonGroup.CreateAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>Passo 4: Criar as pessoas para o Grupo de Pessoas

As pessoas são criadas simultaneamente, e `await WaitCallLimitPerSecondAsync()` também são aplicadas para evitar exceder o limite de chamada.

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

Os rostos adicionados a diferentes pessoas são processados simultaneamente. Os rostos adicionados a uma pessoa específica são processados sequencialmente.
Mais uma vez, `await WaitCallLimitPerSecondAsync()` é invocado para garantir que a frequência do pedido esteja dentro do âmbito de limitação.

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

Neste guia, aprendeu o processo de criar um PersonGroup com um grande número de pessoas e rostos. Alguns lembretes:

- Esta estratégia aplica-se também às Listas de Rostos e Grupos De GrandePessoais.
- Adicionar ou eliminar rostos a diferentes FaceLists ou pessoas em LargePersonGroups são processados simultaneamente.
- Adicionar ou eliminar rostos a uma FaceList específica ou a uma pessoa num LargePersonGroup são feitos sequencialmente.
- Para simplificar, como lidar com uma possível exceção é omitida neste guia. Se quiser aumentar a robustez, aplique a política de relemissão adequada.

Foram explicadas e demonstradas as seguintes características:

- Criar Grupos de Pessoas utilizando o [PersonGroup - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API.
- Criar pessoas utilizando a [Pessoa Grupo Pessoa - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API.
- Adicione rostos às pessoas utilizando a [Pessoa Do Grupo de Pessoas - Adicione a](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API face.

## <a name="related-topics"></a>Tópicos relacionados

- [Detetar rostos numa imagem](HowtoDetectFacesinImage.md)
- [Utilizar a funcionalidade de grande escala](how-to-use-large-scale.md)
