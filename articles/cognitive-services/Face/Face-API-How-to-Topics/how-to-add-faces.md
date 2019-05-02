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
ms.openlocfilehash: 04fe9251ba124ed5d218daf915339c7f84efdeb6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64704190"
---
# <a name="how-to-add-faces-to-a-persongroup"></a>Como adicionar rostos para um PersonGroup

Este guia demonstra as melhores práticas para adicionar um grande número de pessoas e rostos para um objeto de PersonGroup. A mesma estratégia também se aplica a LargePersonGroup FaceList e LargeFaceList. Este exemplo é escrito em C# com a biblioteca de cliente .NET de API de rostos.

## <a name="step-1-initialization"></a>Passo 1: Inicialização

O seguinte código declara várias variáveis e implementa uma função auxiliar para agendar o mostrador adicionar solicitações.

- `PersonCount` é o número total de pessoas.
- `CallLimitPerSecond` é o número máximo de chamadas por segundo de acordo com o escalão de subscrição.
- `_timeStampQueue` é uma Fila para registrar os carimbo de data/hora dos pedidos.
- `await WaitCallLimitPerSecondAsync()` irá aguardar até ser válido para enviar o pedido seguinte.

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

Quando estiver a utilizar uma biblioteca de cliente, tem de passar a chave de subscrição para o construtor da classe FaceServiceClient. Por exemplo:

```csharp
FaceServiceClient faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

A chave de subscrição pode ser obtida da página do Marketplace no portal do Azure. Veja [Subscrições](https://www.microsoft.com/cognitive-services/sign-up).

## <a name="step-3-create-the-persongroup"></a>Passo 3: Criar o PersonGroup

É criado um PersonGroup com o nome "MyPersonGroup" para guardar as pessoas.
A hora do pedido é colocada em fila em `_timeStampQueue` para garantir a validação geral.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceServiceClient.CreatePersonGroupAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-to-the-persongroup"></a>Passo 4: Criar as pessoas para o PersonGroup

São criadas pessoas em simultâneo e `await WaitCallLimitPerSecondAsync()` é também aplicado para evitar exceder o limite de chamadas.

```csharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceServiceClient.CreatePersonAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>Passo 5: Adicionar rostos para as pessoas

A adição de rostos a diferentes pessoas é processada em simultâneo, enquanto para uma pessoa específica é processada sequencialmente.
Uma vez mais, `await WaitCallLimitPerSecondAsync()` é invocado para garantir que a frequência dos pedidos não excede o âmbito de limitação.

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
            await faceServiceClient.AddPersonFaceAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a>Resumo

Neste guia, ficou a conhecer o processo de criação de um PersonGroup com um grande número de pessoas e rostos. Alguns lembretes:

- Esta estratégia também se aplica à FaceList e ao LargePersonGroup.
- A adição/eliminação de rostos de diferentes FaceLists ou Pessoas no LargePersonGroup pode ser processada em simultâneo.
- As mesmas operações para uma FaceList ou Pessoa específica no LargePersonGroup devem ser efetuadas sequencialmente.
- Por uma questão de simplicidade, o processamento de uma potencial exceção foi omitido neste guia. Para obter uma maior robustez, deve ser aplicada uma política de repetição adequada.

Segue-se um lembrete rápido das funcionalidades anteriormente explicadas e demonstradas:

- Criar PersonGroups com a API [PersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
- Criar pessoas com a API [Pessoa de PersonGroup – Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
- Adicionar rostos a pessoas com a API [Pessoa de PersonGroup – Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)

## <a name="related-topics"></a>Tópicos Relacionados

- [Como Identificar Rostos na Imagem](HowtoIdentifyFacesinImage.md)
- [Como Detetar Rostos na Imagem](HowtoDetectFacesinImage.md)
- [Como utilizar a funcionalidade de grande escala](how-to-use-large-scale.md)
