---
title: 'Exemplo: Adicionar faces a um API de Detecção Facial de pessoas'
titleSuffix: Azure Cognitive Services
description: Este guia demonstra como adicionar um grande número de pessoas e rostos a um objeto Person com os serviços cognitivas do Azure API de Detecção Facial.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 2f8a6272b02aea5948be79ddf72d105c4f72bb33
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73744254"
---
# <a name="add-faces-to-a-persongroup"></a>Adicionar faces a um Person

Este guia demonstra como adicionar um grande número de pessoas e rostos a um objeto Person. A mesma estratégia também se aplica aos objetos LargePersonGroup, Facelist e LargeFaceList. Este exemplo é escrito no C# usando os serviços cognitivas do Azure API de detecção facial biblioteca de cliente .net.

## <a name="step-1-initialization"></a>Passo 1: inicialização

O código a seguir declara várias variáveis e implementa uma função auxiliar para agendar as solicitações de adição facial:

- `PersonCount` é o número total de pessoas.
- `CallLimitPerSecond` é o número máximo de chamadas por segundo de acordo com o escalão de subscrição.
- `_timeStampQueue` é uma Fila para registrar os carimbo de data/hora dos pedidos.
- `await WaitCallLimitPerSecondAsync()` aguarda até que seja válido enviar a próxima solicitação.

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

Ao usar uma biblioteca de cliente, você deve passar sua chave de assinatura para o construtor da classe **FaceClient** . Por exemplo:

```csharp
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<SubscriptionKey>"),
    new System.Net.Http.DelegatingHandler[] { });
```

Para obter a chave de assinatura, vá para o Azure Marketplace do portal do Azure. Para obter mais informações, consulte [assinaturas](https://www.microsoft.com/cognitive-services/sign-up).

## <a name="step-3-create-the-persongroup"></a>Passo 3: Criar o PersonGroup

É criado um PersonGroup com o nome "MyPersonGroup" para guardar as pessoas.
A hora do pedido é colocada em fila em `_timeStampQueue` para garantir a validação geral.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceClient.LargePersonGroup.CreateAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>Etapa 4: criar as pessoas para o usuário

As pessoas são criadas simultaneamente e `await WaitCallLimitPerSecondAsync()` também é aplicada para evitar exceder o limite de chamadas.

```csharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceClient.PersonGroupPerson.CreateAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>Passo 5: adicionar rostos às pessoas

As faces adicionadas a diferentes pessoas são processadas simultaneamente. As faces adicionadas para uma pessoa específica são processadas em sequência.
Novamente, `await WaitCallLimitPerSecondAsync()` é invocado para garantir que a frequência de solicitação esteja dentro do escopo de limitação.

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

Neste guia, você aprendeu o processo de criação de um pessoa com um grande número de pessoas e rostos. Alguns lembretes:

- Essa estratégia também se aplica a FaceLists e LargePersonGroups.
- A adição ou a exclusão de faces para diferentes FaceLists ou pessoas em LargePersonGroups são processadas simultaneamente.
- A adição ou a exclusão de faces para uma face específica ou pessoa em um LargePersonGroup são feitas em sequência.
- Para simplificar, como lidar com uma possível exceção é omitido neste guia. Se você quiser aumentar a robustez, aplique a política de repetição apropriada.

Os seguintes recursos foram explicados e demonstrados:

- Crie PersonGroups usando a API de [criação de pessoa-criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) .
- Crie pessoas usando a API [pessoa-criar Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) .
- Adicione faces a pessoas usando a API do [tipo pessoa-adicionar face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) .

## <a name="related-topics"></a>Tópicos relacionados

- [Identificar faces em uma imagem](HowtoIdentifyFacesinImage.md)
- [Detectar faces em uma imagem](HowtoDetectFacesinImage.md)
- [Usar o recurso de grande escala](how-to-use-large-scale.md)
