---
title: 'Exemplo: Use a função de grande escala - Face'
titleSuffix: Azure Cognitive Services
description: Este guia é um artigo sobre como escalar de objetos de PersonGroup e FaceList existentes para objetos LargePersonGroup e LargeFaceList.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.openlocfilehash: dc0964e40e9214e414d865c06006f1d36e97eeb2
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76169770"
---
# <a name="example-use-the-large-scale-feature"></a>Exemplo: Utilize a função em larga escala

Este guia é um artigo avançado sobre como escalar de objetos de PersonGroup e FaceList existentes para objetos LargePersonGroup e LargeFaceList, respectivamente. Este guia demonstra o processo de migração. Assume uma familiaridade básica com objetos PersonGroup e FaceList, a operação [Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) e as funções de reconhecimento facial. Para saber mais sobre estes temas, consulte o guia conceptual de [reconhecimento facial.](../concepts/face-recognition.md)

O LargePersonGroup e o LargeFaceList são coletivamente referidos como operações de grande escala. O LargePersonGroup pode conter até 1 milhão de pessoas, cada uma com um máximo de 248 rostos. A LargeFaceList pode conter até 1 milhão de rostos. As operações em larga escala são semelhantes às pessoas convencionais e faceList, mas têm algumas diferenças devido à nova arquitetura. 

As amostras são escritas em C# usando a biblioteca de clientes Azure Cognitive Services Face.

> [!NOTE]
> Para permitir o desempenho da pesquisa face para identificação e findSimilar em larga escala, introduza uma operação de comboio para pré-processar o LargeFaceList e o LargePersonGroup. O tempo de treino varia de segundos a cerca de meia hora com base na capacidade real. Durante o período de treino, é possível realizar Identificação e FindSimilar se um treino de sucesso foi feito antes. O inconveniente é que as novas pessoas e rostos adicionados não aparecem no resultado até que uma nova migração pós-migração para treino em larga escala seja concluída.

## <a name="step-1-initialize-the-client-object"></a>Passo 1: Inicializar o objeto do cliente

Quando utiliza a biblioteca do cliente Face, a chave de subscrição e o ponto final de subscrição são transmitidos através do construtor da classe FaceClient. Por exemplo:

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
Para mais informações, consulte [Subscrições](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration"></a>Passo 2: Migração de códigos

Esta secção centra-se na forma de migrar a implementação do PersonGroup ou FaceList para o LargePersonGroup ou o LargeFaceList. Embora o LargePersonGroup ou o LargeFaceList difere do PersonGroup ou faceList no design e implementação interna, as interfaces API são semelhantes para a compatibilidade retrógrada.

A migração de dados não é apoiada. Em vez disso, recria o LargePersonGroup ou o LargeFaceList.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>Migrar um PersonGroup para um Grupo de Grandes Pessoas

A migração de um PersonGroup para um LargePersonGroup é simples. Partilham exatamente as mesmas operações a nível de grupo.

Para a implementação do PersonGroup ou da pessoa, é necessário alterar apenas os caminhos API ou classe SDK/módulo para Pessoa sem Grandes Pessoas e Pessoa de Grupo De PessoaS Maiores.

Adicione todos os rostos e pessoas do PersonGroup ao novo LargePersonGroup. Para mais informações, consulte [Adicionar rostos](how-to-add-faces.md).

### <a name="migrate-a-facelist-to-a-largefacelist"></a>Migrar um FaceList para um BigFaceList

| APIs de FaceList | APIs de LargeFaceList |
|:---:|:---:|
| Criar | Criar |
| Eliminar | Eliminar |
| Get | Get |
| Lista | Lista |
| Atualizar | Atualizar |
| - | Preparar |
| - | Obter Estado do Treino |

A tabela anterior é uma comparação das operações ao nível da lista entre FaceList e LargeFaceList. Como mostra, o LargeFaceList vem com novas operações, Treino e Estatuto de Formação, quando comparado com faceList. Treinar o LargeFaceList é uma condição prévia da operação [FindSimilar.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) O treino não é necessário para o FaceList. O seguinte corte é uma função de ajudante para esperar pelo treino de um LargeFaceList:

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

Anteriormente, um uso típico do FaceList com rostos adicionados e FindSimilar parecia o seguinte:

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

Como anteriormente demonstrado, a gestão de dados e a parte FindSimilar são quase as mesmas. A única exceção é que uma nova operação de comboio de pré-processamento deve ser completada no LargeFaceList antes de findSimilar funcionar.

## <a name="step-3-train-suggestions"></a>Passo 3: Sugestões do comboio

Embora a operação train acelere [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) e [Identification,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)o tempo de treino sofre, especialmente quando se chega a grande escala. O tempo estimado de formação em escalas diferentes está listado na tabela seguinte.

| Escala para rostos ou pessoas | Tempo estimado de treino |
|:---:|:---:|
| 1,000 | 1-2 seg |
| 10,000 | 5-10 seg |
| 100 000 | 1-2 min |
| 1 000 000 | 10-30 min |

Para melhor utilizar a funcionalidade em larga escala, recomendamos as seguintes estratégias.

### <a name="step-31-customize-time-interval"></a>Passo 3.1: Personalizar intervalo de tempo

Como é mostrado, `TrainLargeFaceList()`há um intervalo de tempo em milissegundos para atrasar o processo de verificação do estado de treino infinito. Para LargeFaceList com mais rostos, utilizar um intervalo maior reduz o custo e as contagens de chamada. Personalize o intervalo de tempo de acordo com a capacidade esperada do LargeFaceList.

A mesma estratégia aplica-se também ao LargePersonGroup. Por exemplo, quando se treina um LargePersonGroup `timeIntervalInMilliseconds` com 1 milhão de pessoas, pode ser de 60.000, o que é um intervalo de 1 minuto.

### <a name="step-32-small-scale-buffer"></a>Passo 3.2: Tampão de pequena escala

Pessoas ou rostos num LargePersonGroup ou num LargeFaceList só são pesquisáveis após serem treinados. Num cenário dinâmico, novas pessoas ou rostos são constantemente adicionados e devem ser imediatamente pesquisáveis, mas a formação pode demorar mais tempo do que o desejado. 

Para mitigar este problema, utilize um LargePersonGroup ou LargeFaceList extra de pequena escala como tampão apenas para as entradas recém-adicionadas. Este tampão demora menos tempo a treinar devido ao tamanho menor. A capacidade de pesquisa imediata deste tampão temporário deve funcionar. Utilize este tampão em combinação com o treino no master LargePersonGroup ou LargeFaceList executando o treino principal num intervalo de sparser. Exemplos estão a meio da noite e diariamente.

Exemplo de fluxo de trabalho:

1. Crie um master LargePersonGroup ou LargeFaceList, que é a coleção principal. Crie um tampão LargePersonGroup ou LargeFaceList, que é a coleção tampão. A coleção tampão é apenas para pessoas ou rostos recém-adicionados.
1. Adicione novas pessoas ou rostos tanto à coleção principal como à coleção tampão.
1. Apenas treine a coleção tampão com um curto intervalo de tempo para garantir que as entradas recém-adicionadas entrem em vigor.
1. Chamada identificação ou encontre Similar esquecista tanto na coleção principal como na coleção tampão. Fundir os resultados.
1. Quando o tamanho da recolha do tampão aumentar para um limiar ou num tempo de inatividade do sistema, crie uma nova coleção de tampão. Desencadeie a operação do Comboio na coleção principal.
1. Elimine a antiga coleção de tampão após a operação Train terminar na coleção principal.

### <a name="step-33-standalone-training"></a>Passo 3.3: Treino autónomo

Se uma latência relativamente longa for aceitável, não é necessário desencadear a operação do Comboio logo após adicionar novos dados. Em vez disso, a operação de Treino pode ser separada da lógica principal e acionada regularmente. Esta estratégia é adequada para cenários dinâmicos com latência aceitável. Pode ser aplicado em cenários estáticos para reduzir ainda mais a frequência do Comboio.

Suponha que `TrainLargePersonGroup` haja `TrainLargeFaceList`uma função semelhante a. Uma implementação típica da formação autónoma num LargePersonGroup invocando a [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) classe é: `System.Timers`

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

Para obter mais informações sobre gestão de dados e implementações relacionadas com a identificação, consulte [Adicionar rostos](how-to-add-faces.md) e identificar rostos [numa imagem](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a>Resumo

Neste guia, aprendeu a migrar o código PersonGroup ou FaceList existente, não dados, para o LargePersonGroup ou LargeFaceList:

- O LargePersonGroup e o LargeFaceList funcionam semelhantes ao PersonGroup ou faceList, exceto que a operação do Comboio é exigida pela LargeFaceList.
- Tome a estratégia de comboio adequada para a atualização dinâmica de dados para conjuntos de dados em larga escala.

## <a name="next-steps"></a>Passos seguintes

Siga um guia de como aprender a adicionar rostos a um PersonGroup ou execute a operação Identificar num PersonGroup.

- [Adicionar rostos](how-to-add-faces.md)
- [Identificar rostos numa imagem](HowtoIdentifyFacesinImage.md)
