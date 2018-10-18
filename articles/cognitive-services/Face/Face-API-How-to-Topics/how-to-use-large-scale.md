---
title: 'Exemplo: Utilizar a Funcionalidade de Grande Escala – API Face'
titleSuffix: Azure Cognitive Services
description: Utilize a funcionalidade de grande escala na API Face.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: e8bbf78da84ddb77ce956e37f91be46e96144991
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2018
ms.locfileid: "46123084"
---
# <a name="example-how-to-use-the-large-scale-feature"></a>Exemplo: Como utilizar a funcionalidade de grande escala

Este guia é um artigo avançado sobre a migração de código para o aumento vertical de PersonGroup e de FaceList existentes para o LargePersonGroup e o LargeFaceList, respetivamente.
Este guia demonstra o processo de migração com o pressuposto de que tem uma utilização básica de PersonGroup e FaceList.
Para se familiarizar com operações básicas, veja outros tutoriais, por exemplo, [Como identificar rostos em imagens](HowtoIdentifyFacesinImage.md).

A API Face lançou recentemente dois recursos para ativar cenários de grande escala: LargePersonGroup e LargeFaceList, designados coletivamente como operações de Grande escala.
O LargePersonGroup pode conter até 1 000 000 pessoas com um máximo de 248 rostos e o LargeFaceList pode conter até 1 000 000 rostos.

As operações de grande escala são semelhantes aos PersonGroup e FaceList convencionais, mas possuem algumas diferenças notáveis devido à nova arquitetura.
Este guia demonstra o processo de migração com o pressuposto de que tem uma utilização básica de PersonGroup e FaceList.
Os exemplos são escritos em C# com a biblioteca de cliente da API Face.

Para ativar o desempenho de Pesquisa de rostos para Identificação e FindSimilar em grande escala, terá de introduzir uma operação de Treino para processar previamente o LargeFaceList e o LargePersonGroup.
O tempo de treino varia de segundos a cerca de meia hora dependendo da capacidade real.
Durante o período de treino, ainda é possível efetuar a Identificação e o FindSimilar caso um treino bem-sucedido seja efetuado antes.
No entanto, a desvantagem é que os novos rostos/pessoas que foram adicionados não serão apresentados no resultado até que uma nova pós-migração para treino de grande escala esteja concluída.

## <a name="concepts"></a>Conceitos

Se não estiver familiarizado com os seguintes conceitos neste guia, as definições poderão ser encontradas no [glossário](../Glossary.md):

- LargePersonGroup: uma coleção de pessoas com capacidade até 1 000 000.
- LargeFaceList: uma coleção de Rostos com capacidade até 1 000 000.
- Treino: um processo prévio para assegurar o desempenho de Identificação/FindSimilar.
- Identificação: identificar rostos de um ou mais de um PersonGroup ou LargePersonGroup.
- FindSimilar: pesquisar rostos semelhantes de um FaceList ou LargeFaceList.

## <a name="step-1-authorize-the-api-call"></a>Passo 1: Autorizar a chamada da API

Ao utilizar a biblioteca de cliente da API Face, a chave de subscrição e o ponto final de subscrição são transmitidos através do construtor da classe FaceServiceClient. Por exemplo:

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionRegion = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/";
FaceServiceClient FaceServiceClient = new FaceServiceClient(SubscriptionKey, SubscriptionRegion);
```

A chave de subscrição com o ponto final correspondente pode ser obtida da página do Marketplace no portal do Azure.
Veja [Subscrições](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration-in-action"></a>Passo 2: Migração de Código em ação

Esta secção só se concentra em migrar a implementação de PersonGroup/FaceList em LargePersonGroup/LargeFaceList.
Embora LargePersonGroup/LargeFaceList difira de PersonGroup/FaceList na conceção e implementação interna, as interfaces de API são semelhantes para compatibilidade anterior.

Não é suportada a migração de dados. Pelo contrário, tem de recriar o LargePersonGroup/LargeFaceList.

## <a name="step-21-migrate-persongroup-to-largepersongroup"></a>Passo 2.1: Migrar PersonGroup para LargePersonGroup

A migração de PersonGroup para LargePersonGroup é sem problemas dado que partilham exatamente as mesmas operações a nível de grupo.

Para a implementação relacionada com PersonGroup/Person, é apenas necessário alterar os caminhos de API ou a classe/módulo SDK para LargePersonGroup e LargePersonGroup Person.

Em termos de migração de dados, veja [Como Adicionar Rostos](how-to-add-faces.md) para referência.

## <a name="step-22-migrate-facelist-to-largefacelist"></a>Passo 2.2: Migrar FaceList para LargeFaceList

| APIs de FaceList | APIs de LargeFaceList |
|:---:|:---:|
| Criar | Criar |
| Eliminar | Eliminar |
| Get | Get |
| Lista | Lista |
| Atualizar | Atualizar |
| - | Preparar |
| - | Obter Estado do Treino |

A tabela anterior é uma comparação das operações ao nível da lista entre FaceList e LargeFaceList.
Assim como é mostrado, LargeFaceList vem com novas operações, Estado Treinar e Estado Obter Treino, em comparação com FaceList.
Fazer com que LargeFaceList seja treinado é uma pré-condição da operação [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), enquanto não é necessário haver Treino para FaceList.
O fragmento seguinte é uma função auxiliar para aguardar o treino de um LargeFaceList.

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

Anteriormente, uma utilização habitual do FaceList com a adição de rostos e FindSimilar seria

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

Quando migrar para LargeFaceList, ele deve tornar-se

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

Conforme é mostrado acima, a gestão de dados e a parte FindSimilar são quase iguais.
A única exceção é que uma nova operação de Treino de pré-processamento deve ser concluída em LargeFaceList antes de FindSimilar funcionar.

## <a name="step-3-train-suggestions"></a>Passo 3: Treinar Sugestões

Embora a operação de Treinar acelere [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) e [Identificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), o tempo de treino é afetado especialmente quando se trata de grande escala.
O tempo de treino estimado em diferentes escalas está listado na tabela a seguir:

| Dimensionamento (rostos ou pessoas) | Tempo de Treino Estimado |
|:---:|:---:|
| 1,000 | 1-2 s |
| 10,000 | 5-10 s |
| 100 000 | 1 – 2 min |
| 1 000 000 | 10 – 30 min |

Para melhor utilizar a funcionalidade em grande escala, recomenda-se algumas estratégias a levar em consideração.

## <a name="step-31-customize-time-interval"></a>Passo 3.1: Personalizar o Intervalo de Tempo

Tal como é mostrado em `TrainLargeFaceList()`, existe um `timeIntervalInMilliseconds` para atrasar o processo de verificação do estado de treino infinito.
Para LargeFaceList com mais rostos, utilizar um intervalo maior reduz o custo e as contagens de chamada.
O intervalo de tempo deve ser personalizado, de acordo com a expectativa de capacidade do LargeFaceList.

A mesma estratégia também se aplica a LargePersonGroup.
Por exemplo, quando treinar um LargePersonGroup com 1 000 000 pessoas, o `timeIntervalInMilliseconds` poderia ser 60 000 (também conhecido como intervalo de 1 minuto).

## <a name="step-32-small-scale-buffer"></a>Passo 3.2 Memória intermédia de pequena escala

Persons/Faces em LargePersonGroup/LargeFaceList são pesquisáveis apenas depois de haver treino.
Num cenário dinâmico, novos rostos/pessoas são constantemente adicionados e têm de ser imediatamente pesquisáveis, mas o treino pode demorar mais do que o desejado.
Para atenuar este problema, pode utilizar um LargePersonGroup/LargeFaceList extra de pequena escala como uma memória intermédia apenas para as entradas recém-adicionadas.
Esta memória intermédia demora menos tempo a treinar devido a um tamanho muito menor e a pesquisa imediata nessa memória temporária deve funcionar.
Utilize esta memória intermédia em combinação com treino no LargePersonGroup/LargeFaceList principal ao executar o treino no principal num intervalo mais esparso, por exemplo, a meio da noite e diariamente.

Exemplo de fluxo de trabalho:
1. Criar um LargePersonGroup/LargeFaceList principal (coleção principal) e uma memória intermédia LargePersonGroup/LargeFaceList (coleção da memória intermédia). A coleção da memória intermédia é apenas para pessoas/rostos recém-adicionados.
1. Adicionar novos Rostos/Pessoas à coleção principal e coleção de memória intermédia.
1. Treinar apenas a coleção de memória intermédia com um intervalo curto de tempo para garantir que as entradas recém-adicionadas produzem efeito.
1. Chamar Identification/FindSimilar em relação à coleção principal e à coleção de memória intermédia e unir os resultados.
1. Quando o tamanho da coleção de memória intermédia aumenta até um limiar ou num tempo de inatividade do sistema, criar uma nova coleção de memória intermédia e acionar o treino na coleção principal.
1. Eliminar a antiga coleção da memória intermédia após a conclusão de treino na coleção principal.

## <a name="step-33-standalone-training"></a>Passo 3.3 Treino Autónomo

Se uma latência relativamente longa é aceitável, não é necessário acionar a operação de Treino logo após a adição de novos dados.
Em vez disso, a operação de Treino pode ser separada da lógica principal e acionada regularmente.
Esta estratégia é adequada a cenários dinâmicos com latência aceitável e pode ser aplicada a cenários estáticos para reduzir ainda mais a frequência do Treino.

Suponha que haja uma função `TrainLargePersonGroup` semelhante a `TrainLargeFaceList`.
Uma implementação habitual do Treino autónomo em LargePersonGroup ao invocar [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx)
classe em `System.Timers` seria:

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

## <a name="related-topics"></a>Tópicos relacionados

- [Como Identificar Rostos na Imagem](HowtoIdentifyFacesinImage.md)
- [Como Adicionar Rostos](how-to-add-faces.md)
