---
title: 'Exemplo: Utilizar a funcionalidade em grande escala - API Face'
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
ms.openlocfilehash: 35ab2d36a5d6c9977398fdbc16ba22eb1d9656a4
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65229847"
---
# <a name="example-how-to-use-the-large-scale-feature"></a>Exemplo: Como utilizar a funcionalidade em grande escala

Este guia é um artigo avançado sobre como aumentar verticalmente a partir de existente **PersonGroup** e **FaceList** para **LargePersonGroup** e **LargeFaceList**respectivamente. Este guia demonstra o processo de migração e assume uma familiaridade básica com **PersonGroup**, **FaceList**, o [Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) operação e o reconhecimento de rostos funções. Consulte a [reconhecimento facial](../concepts/face-recognition.md) guia conceitual para saber mais sobre eles.

LargePersonGroup e LargeFaceList são coletivamente referidas como operações de grande escala. LargePersonGroup pode conter até 1 000 000 pessoas, cada um com um máximo de 248 rostos, e LargeFaceList pode conter até 1 000 000 rostos. As operações de grande escala são semelhantes aos PersonGroup e FaceList convencional, mas tem algumas diferenças notáveis devido a nova arquitetura. 

Os exemplos foram escritos em C# com a biblioteca de cliente da API Face.

> [!NOTE]
> Para ativar o desempenho de Pesquisa de rostos para Identificação e FindSimilar em grande escala, terá de introduzir uma operação de Treino para processar previamente o LargeFaceList e o LargePersonGroup. O tempo de treino varia de segundos a cerca de meia hora dependendo da capacidade real. Durante o período de treino, ainda é possível efetuar a Identificação e o FindSimilar caso um treino bem-sucedido seja efetuado antes. No entanto, a desvantagem é que os novos rostos/pessoas que foram adicionados não serão apresentados no resultado até que uma nova pós-migração para treino de grande escala esteja concluída.

## <a name="step-1-initialize-the-client-object"></a>Passo 1: Inicializar o objeto de cliente

Ao utilizar a biblioteca de cliente da API Face, a chave de subscrição e o ponto final de subscrição são transmitidos através do construtor da classe FaceServiceClient. Por exemplo:

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionRegion = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/";
FaceServiceClient FaceServiceClient = new FaceServiceClient(SubscriptionKey, SubscriptionRegion);
```

A chave de subscrição com o ponto final correspondente pode ser obtida da página do Marketplace no portal do Azure.
Veja [Subscrições](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration"></a>Passo 2: Migração de código

Esta secção só se concentra em migrar a implementação de PersonGroup/FaceList em LargePersonGroup/LargeFaceList. Embora LargePersonGroup/LargeFaceList difere PersonGroup/FaceList na conceção e implementação interna, as interfaces de API são semelhantes para compatibilidade com versões anteriores.

Não é suportada a migração de dados. Pelo contrário, tem de recriar o LargePersonGroup/LargeFaceList.

### <a name="migrate-persongroup-to-largepersongroup"></a>Migrar PersonGroup para LargePersonGroup

A migração de PersonGroup para LargePersonGroup é simples, que partilham exatamente as mesmas operações de nível de grupo.

Para a implementação relacionada com PersonGroup/Person, é apenas necessário alterar os caminhos de API ou a classe/módulo SDK para LargePersonGroup e LargePersonGroup Person.

Terá de adicionar todos os rostos e as pessoas a partir do PersonGroup para o novo LargePersonGroup. Ver [como adicionar enfrenta](how-to-add-faces.md) para referência.

### <a name="migrate-facelist-to-largefacelist"></a>Migrar FaceList para LargeFaceList

| APIs de FaceList | APIs de LargeFaceList |
|:---:|:---:|
| Criar | Criar |
| Eliminar | Eliminar |
| Obter | Obter |
| Lista | Lista |
| Actualizar | Actualizar |
| - | Treinar |
| - | Obter Estado do Treino |

A tabela anterior é uma comparação das operações ao nível da lista entre FaceList e LargeFaceList. Assim como é mostrado, LargeFaceList vem com novas operações, Estado Treinar e Estado Obter Treino, em comparação com FaceList. Fazer com que LargeFaceList seja treinado é uma pré-condição da operação [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), enquanto não é necessário haver Treino para FaceList. O fragmento seguinte é uma função auxiliar para aguardar o treino de um LargeFaceList.

```CSharp
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
    await FaceServiceClient.TrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await FaceServiceClient.GetLargeFaceListTrainingStatusAsync(largeFaceListId);

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

Anteriormente, um uso típico da FaceList com a adição de rostos e FindSimilar seria semelhante ao seguinte:

```CSharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
FaceServiceClient.CreateFaceListAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await FaceServiceClient.AddFaceToFaceListAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = FaceServiceClient.DetectAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await FaceServiceClient.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

Quando a migração para o LargeFaceList, ele deve se tornar o seguinte:

```CSharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
FaceServiceClient.CreateLargeFaceListAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await FaceServiceClient.AddFaceToLargeFaceListAsync(LargeFaceListId, stream);
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
    var faces = FaceServiceClient.DetectAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await FaceServiceClient.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

Conforme é mostrado acima, a gestão de dados e a parte FindSimilar são quase iguais. A única exceção é que uma nova operação de Treino de pré-processamento deve ser concluída em LargeFaceList antes de FindSimilar funcionar.

## <a name="step-3-train-suggestions"></a>Passo 3: Treinar sugestões

Embora a operação de Treinar acelere [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) e [Identificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), o tempo de treino é afetado especialmente quando se trata de grande escala. O tempo de treino estimado em diferentes escalas está listado na tabela a seguir:

| Dimensionamento (rostos ou pessoas) | Tempo de Treino Estimado |
|:---:|:---:|
| 1,000 | 1-2 s |
| 10,000 | 5-10 s |
| 100,000 | 1 – 2 min |
| 1 000 000 | 10 – 30 min |

Para melhor utilizar a funcionalidade em grande escala, recomenda-se algumas estratégias a levar em consideração.

## <a name="step-31-customize-time-interval"></a>Passo 3.1: Personalizar o intervalo de tempo

Tal como é mostrado em `TrainLargeFaceList()`, existe um `timeIntervalInMilliseconds` para atrasar o processo de verificação do estado de treino infinito. Para LargeFaceList com mais rostos, utilizar um intervalo maior reduz o custo e as contagens de chamada. O intervalo de tempo deve ser personalizado, de acordo com a expectativa de capacidade do LargeFaceList.

A mesma estratégia também se aplica a LargePersonGroup. Por exemplo, quando um LargePersonGroup de treinamento com 1 000 000 pessoas, o `timeIntervalInMilliseconds` poderia ser 60.000 (intervalo de 1 minuto).

## <a name="step-32-small-scale-buffer"></a>Passo 3.2 Memória intermédia de pequena escala

Persons/Faces em LargePersonGroup/LargeFaceList são pesquisáveis apenas depois de haver treino. Num cenário dinâmico, novos rostos/pessoas são constantemente adicionados e têm de ser imediatamente pesquisáveis, mas o treino pode demorar mais do que o desejado. Para atenuar este problema, pode utilizar um LargePersonGroup/LargeFaceList extra de pequena escala como uma memória intermédia apenas para as entradas recém-adicionadas. Esta memória intermédia demora menos tempo a treinar devido a um tamanho muito menor e a pesquisa imediata nessa memória temporária deve funcionar. Utilize esta memória intermédia em combinação com treino no LargePersonGroup/LargeFaceList principal ao executar o treino no principal num intervalo mais esparso, por exemplo, a meio da noite e diariamente.

Exemplo de fluxo de trabalho:
1. Criar um LargePersonGroup/LargeFaceList principal (coleção principal) e uma memória intermédia LargePersonGroup/LargeFaceList (coleção da memória intermédia). A coleção da memória intermédia é apenas para pessoas/rostos recém-adicionados.
1. Adicionar novos Rostos/Pessoas à coleção principal e coleção de memória intermédia.
1. Treinar apenas a coleção de memória intermédia com um intervalo curto de tempo para garantir que as entradas recém-adicionadas produzem efeito.
1. Chamar Identification/FindSimilar em relação à coleção principal e à coleção de memória intermédia e unir os resultados.
1. Quando o tamanho da coleção de memória intermédia aumenta até um limiar ou num tempo de inatividade do sistema, criar uma nova coleção de memória intermédia e acionar o treino na coleção principal.
1. Eliminar a antiga coleção da memória intermédia após a conclusão de treino na coleção principal.

## <a name="step-33-standalone-training"></a>Treinamento do passo 3.3 autónomo

Se uma latência relativamente longa é aceitável, não é necessário acionar a operação de Treino logo após a adição de novos dados. Em vez disso, a operação de Treino pode ser separada da lógica principal e acionada regularmente. Esta estratégia é adequada a cenários dinâmicos com latência aceitável e pode ser aplicada a cenários estáticos para reduzir ainda mais a frequência do Treino.

Suponha que haja uma função `TrainLargePersonGroup` semelhante a `TrainLargeFaceList`. Uma implementação típica do autónoma treinamento em LargePersonGroup invocando o [ `Timer` ](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) classe na `System.Timers` seria:

```CSharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    FaceServiceClient.CreateLargePersonGroupAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Setup a standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1 minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1 hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces and Identification except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

Mais informações sobre a gestão de dados e as implementações relacionadas com a identificação, veja [Como Adicionar Rostos](how-to-add-faces.md) e [Como Identificar Rostos na Imagem](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a>Resumo

Neste guia, aprendeu a migrar o código de PersonGroup/FaceList existente (e não dados) para LargePersonGroup/LargeFaceList:

- LargePersonGroup e LargeFaceList funcionam de maneira semelhante para PersonGroup/FaceList, exceto que a operação Treino é exigida por LargeFaceList.
- Crie uma estratégia de treino adequada para atualização de dados dinâmicos para o conjunto de dados em grande escala.

## <a name="next-steps"></a>Passos Seguintes

Seguir um guia de procedimentos para saber como adicionar rostos para um PersonGroup ou executar a operação de identificar num PersonGroup.

- [Como Adicionar Rostos](how-to-add-faces.md)
- [Como Identificar Rostos na Imagem](HowtoIdentifyFacesinImage.md)