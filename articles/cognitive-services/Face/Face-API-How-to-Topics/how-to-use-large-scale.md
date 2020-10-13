---
title: 'Exemplo: Use a funcionalidade Large-Scale - Face'
titleSuffix: Azure Cognitive Services
description: Este guia é um artigo sobre como escalar de objetos persongroup e FaceList existentes para objetos LargePersonGroup e LargeFaceList.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 5341c2613624c6a52f1649dcd8a64b6746b84f67
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91332394"
---
# <a name="example-use-the-large-scale-feature"></a>Exemplo: Utilize a característica em larga escala

Este guia é um artigo avançado sobre como escalar de objetos persongroup e FaceList existentes para objetos LargePersonGroup e LargeFaceList, respectivamente. Este guia demonstra o processo de migração. Assume uma familiaridade básica com objetos PersonGroup e FaceList, a operação [Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) e as funções de reconhecimento facial. Para saber mais sobre estes temas, consulte o guia conceptual de [reconhecimento facial.](../concepts/face-recognition.md)

O LargePersonGroup e o LargeFaceList são colectivamente referidos como operações em larga escala. O LargePersonGroup pode conter até 1 milhão de pessoas, cada uma com um máximo de 248 faces. O BigFaceList pode conter até 1 milhão de rostos. As operações em larga escala são semelhantes às convencionais PersonGroup e FaceList, mas têm algumas diferenças devido à nova arquitetura. 

As amostras são escritas em C# utilizando a biblioteca de clientes Azure Cognitive Services Face.

> [!NOTE]
> Para ativar o desempenho da pesquisa face para identificação e FindSimilar em larga escala, introduza uma operação Train para pré-processar o LargeFaceList e o LargePersonGroup. O tempo de treino varia de segundos a cerca de meia hora com base na capacidade real. Durante o período de treino, é possível realizar Identificação e FindSimilar se um treino bem sucedido foi feito antes. O inconveniente é que as novas pessoas e rostos adicionados não aparecem no resultado até que uma nova migração pós-migração para o treino em larga escala seja concluída.

## <a name="step-1-initialize-the-client-object"></a>Passo 1: Inicializar o objeto do cliente

Quando utiliza a biblioteca do cliente Face, a chave de subscrição e o ponto final de subscrição são passados através do construtor da classe FaceClient. Por exemplo:

```csharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

Para obter a chave de subscrição com o seu ponto final correspondente, vá ao Mercado Azure a partir do portal Azure.
Para mais informações, consulte [Subscrições.](https://azure.microsoft.com/services/cognitive-services/directory/vision/)

## <a name="step-2-code-migration"></a>Passo 2: Migração de códigos

Esta secção centra-se em como migrar a implementação do PersonGroup ou faceList para o LargePersonGroup ou largeFaceList. Embora o LargePersonGroup ou o LargeFaceList diferam do PersonGroup ou faceList em design e implementação interna, as interfaces API são semelhantes para compatibilidade retrógrada.

A migração de dados não é apoiada. Em vez disso, recria o LargePersonGroup ou o LargeFaceList.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>Migrar um PersonGroup para um LargePersonGroup

A migração de um PersonGroup para um LargePersonGroup é simples. Partilham exatamente as mesmas operações de grupo.

Para a implementação do PersonGroup ou da pessoa, é necessário alterar apenas os caminhos da API ou classe/módulo SDK para o LargePersonGroup e o LargePersonGroup Person.

Adicione todos os rostos e pessoas do PersonGroup ao novo LargePersonGroup. Para mais informações, consulte [Adicionar faces.](how-to-add-faces.md)

### <a name="migrate-a-facelist-to-a-largefacelist"></a>Migrar uma Lista de Rostos para uma Grande Lista de Rostos

| APIs de FaceList | APIs de LargeFaceList |
|:---:|:---:|
| Criar | Criar |
| Eliminar | Eliminar |
| Get | Get |
| Lista | Lista |
| Atualizar | Atualizar |
| - | Preparar |
| - | Obter Estado do Treino |

A tabela anterior é uma comparação das operações ao nível da lista entre FaceList e LargeFaceList. Como é mostrado, o LargeFaceList vem com novas operações, Train and Get Training Status, quando comparado com o FaceList. Treinar a LargeFaceList é uma condição prévia da operação [FindSimilar.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) O treino não é necessário para a FaceList. O seguinte corte é uma função de ajudante para aguardar o treino de uma BigFaceList:

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

Anteriormente, um uso típico de FaceList com rostos adicionados e FindSimilar parecia o seguinte:

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

Ao migrar para a LargeFaceList, torna-se o seguinte:

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

Como anteriormente demonstrado, a gestão de dados e a parte FindSimilar são quase as mesmas. A única exceção é que uma nova operação de comboio pré-processamento deve ser concluída na LargeFaceList antes de findSimilar funcionar.

## <a name="step-3-train-suggestions"></a>Passo 3: Sugestões de comboio

Embora a operação Train acelere [o FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) e [Identification,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)o tempo de treino sofre, especialmente quando se chega a grande escala. O tempo estimado de treino em escalas diferentes está listado na tabela seguinte.

| Escala para rostos ou pessoas | Tempo estimado de treino |
|:---:|:---:|
| 1,000 | 1-2 seg |
| 10,000 | 5-10 segundos |
| 100.000 | 1-2 min |
| 1 000 000 | 10-30 min |

Para melhor utilizar a característica em larga escala, recomendamos as seguintes estratégias.

### <a name="step-31-customize-time-interval"></a>Passo 3.1: Personalizar intervalo de tempo

Como é mostrado em `TrainLargeFaceList()` , há um intervalo de tempo em milissegundos para atrasar o processo de verificação do estado de treino infinito. Para LargeFaceList com mais rostos, utilizar um intervalo maior reduz o custo e as contagens de chamada. Personalize o intervalo de tempo de acordo com a capacidade esperada da LargeFaceList.

A mesma estratégia aplica-se também ao LargePersonGroup. Por exemplo, quando se treina um LargePersonGroup com 1 milhão de pessoas, `timeIntervalInMilliseconds` pode ser de 60.000, o que é um intervalo de 1 minuto.

### <a name="step-32-small-scale-buffer"></a>Passo 3.2: Tampão de pequena escala

Pessoas ou rostos num LargePersonGroup ou num LargeFaceList só são pesjáveis depois de terem sido treinados. Num cenário dinâmico, novas pessoas ou rostos são constantemente adicionados e devem ser imediatamente pescáveis, mas o treino pode demorar mais tempo do que o desejado. 

Para mitigar este problema, utilize um LargePersonGroup ou LargeFaceList extra em pequena escala como tampão apenas para as entradas recém-adicionadas. Este tampão demora menos tempo a treinar devido ao tamanho mais pequeno. A capacidade imediata de pesquisa neste tampão temporário deve funcionar. Utilize este tampão em combinação com o treino no Master LargePersonGroup ou largeFaceList executando o treino principal num intervalo mais escasso. Exemplos estão no meio da noite e diariamente.

Exemplo de fluxo de trabalho:

1. Crie um Master LargePersonGroup ou LargeFaceList, que é a coleção principal. Crie um buffer LargePersonGroup ou LargeFaceList, que é a coleção de tampão. A recolha de tampão destina-se apenas a pessoas ou rostos recém-adicionados.
1. Adicione novas pessoas ou rostos tanto à coleção principal como à coleção de tampão.
1. Treine apenas a recolha do tampão com um curto intervalo de tempo para garantir que as entradas recém-adicionadas entrem em vigor.
1. Ligue para identificação ou FindSimilar contra a coleção principal e a coleção de tampão. Misture os resultados.
1. Quando o tamanho da recolha do tampão aumentar para um limiar ou em tempo de inatividade do sistema, crie uma nova coleção de tampão. Desencadeie a operação train na coleção principal.
1. Apague a antiga coleção de tampão após o fim da operação Train na coleção principal.

### <a name="step-33-standalone-training"></a>Passo 3.3: Treino autónomo

Se uma latência relativamente longa for aceitável, não é necessário desencadear a operação train logo após a sua adição de novos dados. Em vez disso, a operação de Treino pode ser separada da lógica principal e acionada regularmente. Esta estratégia é adequada para cenários dinâmicos com latência aceitável. Pode ser aplicado a cenários estáticos para reduzir ainda mais a frequência do comboio.

Suponha que haja uma `TrainLargePersonGroup` função semelhante `TrainLargeFaceList` a. Uma implementação típica do treino autónomo num LargePersonGroup invocando a [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) classe `System.Timers` em:

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

Para obter mais informações sobre gestão de dados e implementações relacionadas com a identificação, consulte [Add faces](how-to-add-faces.md).

## <a name="summary"></a>Resumo

Neste guia, aprendeu a migrar o código PersonGroup ou FaceList existente, não dados, para o LargePersonGroup ou LargeFaceList:

- O LargePersonGroup e o LargeFaceList funcionam semelhantes ao PersonGroup ou FaceList, exceto que a operação Train é necessária pela LargeFaceList.
- Leve a estratégia adequada do comboio para uma atualização dinâmica de dados para conjuntos de dados em larga escala.

## <a name="next-steps"></a>Passos seguintes

Siga um guia de como adicionar rostos a um PersonGroup ou escreva um script para fazer a operação Identificar num Grupo de Pessoas.

- [Adicionar rostos](how-to-add-faces.md)
- [Face cliente biblioteca quickstart](../Quickstarts/client-libraries.md)