---
title: 'Exemplo: Adicionar rostos para um PersonGroup - Face API'
titleSuffix: Azure Cognitive Services
description: Utilize a API Face para adicionar rostos em imagens.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 0415dcae08c188c1758150c4b8b0df4dee014ce6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448609"
---
# <a name="add-faces-to-a-persongroup"></a>Adicionar rostos para um PersonGroup

Este guia demonstra como adicionar um grande número de pessoas e rostos para um objeto de PersonGroup. A mesma estratégia também se aplica a objetos LargePersonGroup FaceList e LargeFaceList. Este exemplo é escrito em C# com a biblioteca de cliente .NET de API de rostos do Azure Cognitive Services.

## <a name="step-1-initialization"></a>Passo 1: Inicialização

O seguinte código declara várias variáveis e implementa uma função auxiliar para agendar o mostrador adicionar pedidos:

- `PersonCount` é o número total de pessoas.
- `CallLimitPerSecond` é o número máximo de chamadas por segundo de acordo com o escalão de subscrição.
- `_timeStampQueue` é uma Fila para registrar os carimbo de data/hora dos pedidos.
- `await WaitCallLimitPerSecondAsync()` tem de aguardar até que seja válido para enviar o pedido seguinte.

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

## <a name="step-2-authorize-the-api-call"></a>Passo 2: Autorizar a chamada de API

Quando utiliza uma biblioteca de cliente, tem de passar a chave de subscrição para o construtor do **FaceClient** classe. Por exemplo:

```csharp
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<SubscriptionKey>"),
    new System.Net.Http.DelegatingHandler[] { });
```

Para obter a chave de subscrição, vá para o Azure Marketplace no portal do Azure. Para obter mais informações, consulte [subscrições](https://www.microsoft.com/cognitive-services/sign-up).

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

Pessoas são criadas ao mesmo tempo, e `await WaitCallLimitPerSecondAsync()` também é aplicado para evitar exceder o limite de chamada.

```csharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceClient.PersonGroupPerson.CreateAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>Passo 5: Adicionar rostos para as pessoas

Rostos adicionados para pessoas diferentes são processados em simultâneo. Rostos adicionados para uma pessoa específica são processados sequencialmente.
Novamente, `await WaitCallLimitPerSecondAsync()` é invocado para se certificar de que a frequência de solicitação é dentro do escopo de limitação.

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

Neste guia, aprendeu o processo de criação de um PersonGroup com um grande número de pessoas e rostos. Alguns lembretes:

- Esta estratégia também se aplica a FaceLists e LargePersonGroups.
- Adicionar ou excluir os rostos de pessoas em LargePersonGroups ou FaceLists diferente são processadas em simultâneo.
- Adicionar ou excluir rostos para uma pessoa num LargePersonGroup ou FaceList específicos são efetuadas sequencialmente.
- Para simplificar, como lidar com uma exceção de potencial for omitido neste guia. Se quer aprimorar mais robustez, aplica a política de repetição adequada.

As seguintes funcionalidades foram explicadas e demonstrei:

- Criar PersonGroups utilizando o [PersonGroup - criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API.
- Criar a pessoas ao utilizar o [PersonGroup pessoa - criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API.
- Adicionar rostos a pessoas ao utilizar o [PersonGroup pessoa - adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API.

## <a name="related-topics"></a>Tópicos relacionados

- [Identificar rostos numa imagem](HowtoIdentifyFacesinImage.md)
- [Detetar rostos numa imagem](HowtoDetectFacesinImage.md)
- [Utilizar a funcionalidade em grande escala](how-to-use-large-scale.md)
