---
title: 'Exemplo: Utilizar a funcionalidade em grande escala - Face API'
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
ms.openlocfilehash: dcbec817f771324219a68de96eb5dd262a887fc1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449049"
---
# <a name="example-use-the-large-scale-feature"></a>Exemplo: Utilizar a funcionalidade de grande escala

Este guia é um artigo avançado sobre como a ampliação de objetos existentes de PersonGroup e FaceList para objetos LargePersonGroup e LargeFaceList, respectivamente. Este guia demonstra o processo de migração. Ele supõe que uma familiaridade básica com objetos PersonGroup e FaceList, o [Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) operação e as funções de reconhecimento de rostos. Para saber mais sobre estes tópicos, consulte a [reconhecimento facial](../concepts/face-recognition.md) guia conceitual.

LargePersonGroup e LargeFaceList são coletivamente referidas como operações de grande escala. LargePersonGroup pode conter até 1 milhão de pessoas, cada um com um máximo de 248 rostos. LargeFaceList pode conter até 1 milhão de rostos. As operações de grande escala são semelhantes aos PersonGroup e FaceList convencional, mas tem algumas diferenças devido a nova arquitetura. 

Os exemplos são escritos em C# com a biblioteca de cliente de API de Face de serviços cognitivos do Azure.

> [!NOTE]
> Para ativar o desempenho de pesquisa de rostos para identificação e FindSimilar em grande escala, apresentam uma operação de Train pré-processar os LargeFaceList e LargePersonGroup. O tempo de treinamento varia de segundos para cerca de meia hora com base na capacidade real. Durante o período de formação, é possível realizar a identificação e FindSimilar se uma operação de treinamento bem-sucedido foi feita antes. A desvantagem é que as pessoas foram adicionadas novos e rostos não são apresentados no resultado até é concluída uma nova migração de postagem para a formação de larga escala.

## <a name="step-1-initialize-the-client-object"></a>Passo 1: Inicializar o objeto de cliente

Quando utiliza a biblioteca de cliente de Face API, a chave de subscrição e o ponto final de subscrição são transmitidos através do construtor da classe FaceClient. Por exemplo:

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

Para obter a chave de subscrição com o seu ponto de extremidade correspondente, vá para o Azure Marketplace no portal do Azure.
Para obter mais informações, consulte [subscrições](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration"></a>Passo 2: Migração de código

Esta seção se concentra em como migrar a implementação de PersonGroup ou FaceList LargePersonGroup ou LargeFaceList. Embora LargePersonGroup ou LargeFaceList difere PersonGroup ou FaceList na conceção e implementação interna, as interfaces de API são semelhantes para compatibilidade com versões anteriores.

Não é suportada a migração de dados. Voltar a criar o LargePersonGroup ou LargeFaceList em vez disso.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>Migrar uma PersonGroup para um LargePersonGroup

A migração de um PersonGroup para um LargePersonGroup é simples. Partilham exatamente as mesmas operações de nível de grupo.

Por PersonGroup ou pessoa-relacionados com a implementação, é necessário alterar apenas os caminhos de API ou classe/módulo SDK LargePersonGroup e LargePersonGroup pessoa.

Adicione todos os rostos e pessoas do PersonGroup para o novo LargePersonGroup. Para obter mais informações, consulte [adicionar rostos](how-to-add-faces.md).

### <a name="migrate-a-facelist-to-a-largefacelist"></a>Migrar uma FaceList para um LargeFaceList

| APIs de FaceList | APIs de LargeFaceList |
|:---:|:---:|
| Criar | Criar |
| Eliminar | Eliminar |
| Get | Get |
| Lista | Lista |
| Atualizar | Atualizar |
| - | Preparar |
| - | Obter Estado do Treino |

A tabela anterior é uma comparação das operações ao nível da lista entre FaceList e LargeFaceList. Como é mostrado, LargeFaceList vem com novas operações, Train e obter o estado de treinamento, em comparação com FaceList. O LargeFaceList de preparação é uma pré-condição para o [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) operação. Treinamento não é necessário para FaceList. O fragmento seguinte é uma função auxiliar para aguardar a formação de um LargeFaceList:

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

Um uso típico de FaceList com rostos adicionados e FindSimilar parecia anteriormente, semelhante ao seguinte:

```CSharp
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

Quando a migração para o LargeFaceList, torna-se ao seguinte:

```CSharp
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

Como a mostrada anteriormente, a gestão de dados e a parte FindSimilar são quase os mesmos. A única exceção é que uma nova operação pré-processamento deve ser concluída no LargeFaceList antes de FindSimilar funciona.

## <a name="step-3-train-suggestions"></a>Passo 3: Treinar sugestões

Embora a operação de Train acelera [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) e [identificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), o tempo de treinamento sofre, especialmente quando a chegar ao dimensionamento de grande escala. O tempo de treinamento estimado em diferentes escalas está listado na tabela seguinte.

| Escala para rostos ou as pessoas | Tempo estimado de treinamento |
|:---:|:---:|
| 1,000 | seg de 1 a 2 |
| 10,000 | seg de 5 a 10 |
| 100,000 | 1-2 min |
| 1 000 000 | 10-30 min |

Para utilizar melhor a funcionalidade em grande escala, recomendamos as estratégias a seguir.

### <a name="step-31-customize-time-interval"></a>Passo 3.1: Personalizar o intervalo de tempo

Como é mostrado na `TrainLargeFaceList()`, existe um intervalo de tempo em milissegundos para atrasar o estado de treinamento infinito a verificar o processo. Para LargeFaceList com mais rostos, utilizar um intervalo maior reduz o custo e as contagens de chamada. Personalize o intervalo de tempo, de acordo com a expectativa de capacidade do LargeFaceList.

A mesma estratégia também se aplica a LargePersonGroup. Por exemplo, quando preparar um LargePersonGroup com 1 milhão de pessoas, `timeIntervalInMilliseconds` poderá ser 60.000, que é um intervalo de 1 minuto.

### <a name="step-32-small-scale-buffer"></a>Passo 3.2: Memória intermédia de pequena escala

Pessoas ou de rostos num LargePersonGroup ou um LargeFaceList são pesquisáveis apenas depois de a ser preparado. Num cenário dinâmico, novas pessoas ou rostos são constantemente adicionados e tem de ser imediatamente pesquisáveis, ainda treinamento pode demorar mais tempo do que o desejado. 

Para atenuar este problema, utilize um extra pequena escala LargePersonGroup ou LargeFaceList como uma memória intermédia apenas para as entradas recém-adicionada. Esse buffer demora tempo mais curto para treinar devido ao tamanho mais pequeno. O recurso de pesquisa de imediato desse buffer temporário deve funcionar. Use esse buffer em combinação com a formação do mestre LargePersonGroup ou LargeFaceList ao executar o treinamento principal num intervalo mais esparso. Os exemplos são no meio da noite e diariamente.

Exemplo de fluxo de trabalho:

1. Crie um principal LargePersonGroup ou LargeFaceList, que é a coleção principal. Crie uma memória intermédia LargePersonGroup ou LargeFaceList, que é a coleção de memória intermédia. A coleção de memória intermédia é apenas para pessoas recentemente adicionadas ou rostos.
1. Adicione novo pessoas ou rostos para a coleção principal e a coleção de memória intermédia.
1. Treine apenas a coleção de memória intermédia com um intervalo curto período de tempo para se certificar de que as entradas recém-adicionada entrem em vigor.
1. Identificação de chamada ou FindSimilar contra a coleção principal e a coleção de memória intermédia. Intercale os resultados.
1. Quando aumenta o tamanho de coleção de memória intermédia para um limiar ou num tempo de inatividade do sistema, crie uma nova coleção de memória intermédia. Acione a operação na coleção principal.
1. Elimine a coleção de memória intermédia antigo depois de concluída a operação de Train na coleção principal.

### <a name="step-33-standalone-training"></a>Passo 3.3: Treinamento de autónomo

Se uma latência relativamente longa é aceitável, não é necessário para a operação de formação correta de Acionador depois de adicionar novos dados. Em vez disso, a operação de Treino pode ser separada da lógica principal e acionada regularmente. Esta estratégia é adequada a cenários dinâmicos com latência aceitável. Ele pode ser aplicado aos cenários de estáticos para reduzir ainda mais a frequência de comboio.

Suponha que haja uma `TrainLargePersonGroup` função semelhante ao `TrainLargeFaceList`. Uma implementação típica do treinamento autónomo num LargePersonGroup invocando o [ `Timer` ](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) classe na `System.Timers` é:

```CSharp
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

Para obter mais informações sobre implementações relacionadas com a identificação e o gerenciamento de dados, consulte [adicionar rostos](how-to-add-faces.md) e [identifique rostos numa imagem](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a>Resumo

Neste guia, aprendeu a migrar o código de PersonGroup ou FaceList existente, não dados, para o LargePersonGroup ou LargeFaceList:

- LargePersonGroup e LargeFaceList trabalham semelhante a PersonGroup ou FaceList, exceto pelo fato da operação de comboio é exigida pelo LargeFaceList.
- Efetuar a estratégia de Train adequada para atualização de dados dinâmicos para conjuntos de dados em grande escala.

## <a name="next-steps"></a>Passos Seguintes

Seguir um guia de procedimentos para saber como adicionar rostos para um PersonGroup ou executar a operação de identificar num PersonGroup.

- [Adicionar rostos](how-to-add-faces.md)
- [Identificar rostos numa imagem](HowtoIdentifyFacesinImage.md)