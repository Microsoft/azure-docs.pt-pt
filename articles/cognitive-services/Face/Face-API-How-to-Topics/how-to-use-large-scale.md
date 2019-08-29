---
title: 'Exemplo: Usar o recurso de grande escala-API de Detecção Facial'
titleSuffix: Azure Cognitive Services
description: Utilize a funcionalidade de grande escala na API Face.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.openlocfilehash: d8ecfb53b78277e4b0e4a85d60fb6712d0bc2292
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114838"
---
# <a name="example-use-the-large-scale-feature"></a>Exemplo: Utilizar a funcionalidade de grande escala

Este guia é um artigo avançado sobre como escalar verticalmente de objetos Person e Facelist existentes para objetos LargePersonGroup e LargeFaceList, respectivamente. Este guia demonstra o processo de migração. Ele assume uma familiaridade básica com os objetos Person e Facelist, a operação de [treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) e as funções de reconhecimento facial. Para saber mais sobre esses assuntos, consulte o guia conceitual de [reconhecimento facial](../concepts/face-recognition.md) .

LargePersonGroup e LargeFaceList são coletivamente chamados de operações em larga escala. LargePersonGroup pode conter até 1 milhão pessoas, cada uma com um máximo de 248 faces. LargeFaceList pode conter até 1 milhão rostos. As operações em grande escala são semelhantes às de Person e face convencionais, mas têm algumas diferenças devido à nova arquitetura. 

Os exemplos são gravados C# no usando os serviços cognitivas do Azure API de detecção facial biblioteca de cliente.

> [!NOTE]
> Para habilitar o desempenho de pesquisa facial para identificação e FindSimilar em grande escala, apresente uma operação de treinamento para pré-processar o LargeFaceList e o LargePersonGroup. O tempo de treinamento varia de segundos a cerca de meia hora com base na capacidade real. Durante o período de treinamento, é possível executar a identificação e o FindSimilar se um operando de treinamento bem-sucedido tiver sido feito antes. A desvantagem é que as novas pessoas e rostos adicionadas não aparecem no resultado até que uma nova migração após a conclusão do treinamento em larga escala seja concluída.

## <a name="step-1-initialize-the-client-object"></a>Passo 1: Inicializar o objeto de cliente

Quando você usa a biblioteca de cliente API de Detecção Facial, a chave de assinatura e o ponto de extremidade de assinatura são passados por meio do construtor da classe FaceClient. Por exemplo:

```csharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

Para obter a chave de assinatura com seu ponto de extremidade correspondente, vá para o Azure Marketplace do portal do Azure.
Para obter mais informações, consulte [assinaturas](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration"></a>Passo 2: Migração de código

Esta seção concentra-se em como migrar a implementação de Person ou de Facelist para LargePersonGroup ou LargeFaceList. Embora LargePersonGroup ou LargeFaceList seja diferente de Person ou Facelist no design e na implementação interna, as interfaces de API são semelhantes para compatibilidade com versões anteriores.

Não há suporte para a migração de dados. Em vez disso, recrie o LargePersonGroup ou o LargeFaceList.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>Migrar um Person para um LargePersonGroup

A migração de um Person para um LargePersonGroup é simples. Eles compartilham exatamente as mesmas operações em nível de grupo.

Para a implementação relacionada a Person ou Person, é necessário alterar apenas os caminhos de API ou a classe/módulo do SDK para LargePersonGroup e LargePersonGroup Person.

Adicione todas as faces e pessoas do Person ao novo LargePersonGroup. Para obter mais informações, consulte [Adicionar faces](how-to-add-faces.md).

### <a name="migrate-a-facelist-to-a-largefacelist"></a>Migrar uma Facelist para um LargeFaceList

| APIs de FaceList | APIs de LargeFaceList |
|:---:|:---:|
| Criar | Criar |
| Eliminar | Eliminar |
| Obter | Obter |
| Lista | Lista |
| Atualizar | Atualizar |
| - | Preparar |
| - | Obter Estado do Treino |

A tabela anterior é uma comparação das operações ao nível da lista entre FaceList e LargeFaceList. Como é mostrado, o LargeFaceList vem com novas operações, treina e Obtém o status de treinamento, quando comparado com a Facelist. Treinar o LargeFaceList é uma pré-condição da operação [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) . O treinamento não é necessário para a Facelist. O trecho a seguir é uma função auxiliar para aguardar o treinamento de um LargeFaceList:

```csharp
/// <summary>
/// Helper function to train LargeFaceList and wait for finish.
/// </summary>
/// <remarks>
/// The time interval can be adjusted considering the following factors:
/// - The training time which depends on the capacity of the LargeFaceList.
/// - The acceptable latency for getting the training status.
/// - The call frequency and cost.
///
/// Estimated training time for LargeFaceList in different scale:
/// -     1,000 faces cost about  1 to  2 seconds.
/// -    10,000 faces cost about  5 to 10 seconds.
/// -   100,000 faces cost about  1 to  2 minutes.
/// - 1,000,000 faces cost about 10 to 30 minutes.
/// </remarks>
/// <param name="largeFaceListId">The Id of the LargeFaceList for training.</param>
/// <param name="timeIntervalInMilliseconds">The time interval for getting training status in milliseconds.</param>
/// <returns>A task of waiting for LargeFaceList training finish.</returns>
private static async Task TrainLargeFaceList(
    string largeFaceListId,
    int timeIntervalInMilliseconds = 1000)
{
    // Trigger a train call.
    await FaceClient.LargeTrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await faceClient.LargeFaceList.TrainAsync(largeFaceListId);

        if (status.Status == Status.Running)
        {
            continue;
        }
        else if (status.Status == Status.Succeeded)
        {
            break;
        }
        else
        {
            throw new Exception("The train operation is failed!");
        }
    }
}
```

Anteriormente, um uso típico de Facelist com faces adicionadas e FindSimilar era semelhante ao seguinte:

```csharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.FaceList.CreateAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.FaceList.AddFaceFromStreamAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

Ao migrá-lo para o LargeFaceList, ele se torna o seguinte:

```csharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.LargeFaceList.CreateAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.LargeFaceList.AddFaceFromStreamAsync(LargeFaceListId, stream);
            }
        });

// Train() is newly added operation for LargeFaceList.
// Must call it before FindSimilarAsync() to ensure the newly added faces searchable.
await TrainLargeFaceList(LargeFaceListId);

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

Como mostrado anteriormente, o gerenciamento de dados e a parte FindSimilar são quase iguais. A única exceção é que uma operação de treinamento de pré-processamento nova deve ser concluída no LargeFaceList antes de o FindSimilar funcionar.

## <a name="step-3-train-suggestions"></a>Passo 3: Sugestões de treinamento

Embora a operação treinar acelera o [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) e a [identificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), o tempo de treinamento sofre, especialmente ao chegar em grande escala. O tempo de treinamento estimado em escalas diferentes é listado na tabela a seguir.

| Escala para rostos ou pessoas | Tempo de treinamento estimado |
|:---:|:---:|
| 1,000 | 1-2 s |
| 10,000 | 5-10 s |
| 100,000 | mínimo de 1-2 |
| 1 000 000 | mínimo de 10-30 |

Para utilizar melhor o recurso de grande escala, recomendamos as estratégias a seguir.

### <a name="step-31-customize-time-interval"></a>Etapa 3,1: Personalizar intervalo de tempo

Como é mostrado em `TrainLargeFaceList()`, há um intervalo de tempo em milissegundos para atrasar o processo de verificação de status de treinamento infinito. Para LargeFaceList com mais rostos, utilizar um intervalo maior reduz o custo e as contagens de chamada. Personalize o intervalo de tempo de acordo com a capacidade esperada do LargeFaceList.

A mesma estratégia também se aplica a LargePersonGroup. Por exemplo, quando você treina um LargePersonGroup com 1 milhão pessoas, `timeIntervalInMilliseconds` pode ser 60.000, que é um intervalo de 1 minuto.

### <a name="step-32-small-scale-buffer"></a>Etapa 3,2: Buffer de pequena escala

As pessoas ou rostos em um LargePersonGroup ou LargeFaceList são pesquisáveis somente depois de serem treinados. Em um cenário dinâmico, novas pessoas ou rostos são constantemente adicionadas e devem ser pesquisadas imediatamente, mas o treinamento pode levar mais tempo do que o desejado. 

Para atenuar esse problema, use um LargePersonGroup de pequena escala extra ou LargeFaceList como um buffer somente para as entradas adicionadas recentemente. Esse buffer leva um tempo mais curto para treinar devido ao tamanho menor. A funcionalidade de pesquisa imediata nesse buffer temporário deve funcionar. Use esse buffer em combinação com o treinamento no mestre LargePersonGroup ou LargeFaceList executando o treinamento mestre em um intervalo mais esparso. Os exemplos estão no meio da noite e diariamente.

Exemplo de fluxo de trabalho:

1. Crie um LargePersonGroup mestre ou LargeFaceList, que é a coleção mestra. Crie um buffer LargePersonGroup ou LargeFaceList, que é a coleção de buffers. A coleção de buffers é apenas para pessoas ou rostos adicionadas recentemente.
1. Adicione novas pessoas ou rostos à coleção mestre e à coleção de buffers.
1. Treine a coleção de buffers com um intervalo de tempo curto para garantir que as entradas adicionadas recentemente tenham efeito.
1. Chame a identificação ou FindSimilar em relação à coleção mestre e à coleção de buffers. Mescle os resultados.
1. Quando o tamanho da coleção de buffers aumenta para um limite ou em um tempo ocioso do sistema, crie uma nova coleção de buffers. Disparar a operação de treinamento na coleção mestra.
1. Exclua a coleção de buffers antiga depois que a operação de treinamento for concluída na coleção mestre.

### <a name="step-33-standalone-training"></a>Etapa 3,3: Treinamento autônomo

Se uma latência relativamente longa for aceitável, não será necessário disparar a operação de treinamento logo após a adição de novos dados. Em vez disso, a operação de Treino pode ser separada da lógica principal e acionada regularmente. Essa estratégia é adequada para cenários dinâmicos com latência aceitável. Ele pode ser aplicado a cenários estáticos para reduzir ainda mais a frequência de treinamento.

Suponha que haja uma `TrainLargePersonGroup` função semelhante a `TrainLargeFaceList`. Uma implementação típica do treinamento autônomo em um LargePersonGroup invocando a [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) classe no `System.Timers` é:

```csharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    faceClient.LargePersonGroup.CreateAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Set up standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1-minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1-hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces, and identification, except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

Para obter mais informações sobre o gerenciamento de dados e as implementações relacionadas à identificação, consulte [Adicionar faces](how-to-add-faces.md) e [identificar faces em uma imagem](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a>Resumo

Neste guia, você aprendeu a migrar o código Person ou o tipo de Facelist existente, e não os dados, para o LargePersonGroup ou LargeFaceList:

- LargePersonGroup e LargeFaceList funcionam de forma semelhante a Person ou Facelist, exceto que a operação de treinamento é exigida pelo LargeFaceList.
- Faça a estratégia de treinamento adequada para a atualização dinâmica de dados para conjuntos de dados em larga escala.

## <a name="next-steps"></a>Passos Seguintes

Siga um guia de instruções para saber como adicionar faces a um Person ou executar a operação de identificação em um Person.

- [Adicionar faces](how-to-add-faces.md)
- [Identificar faces em uma imagem](HowtoIdentifyFacesinImage.md)
