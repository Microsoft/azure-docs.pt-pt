---
title: Migrar os seus dados faciais através de subscrições - Face
titleSuffix: Azure Cognitive Services
description: Este guia mostra-lhe como migrar os dados do seu rosto armazenadode de uma subscrição Face para outra.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: nitinme
ms.openlocfilehash: fd0e7079b3b70a6a6b8166cc7fc7518070e7153d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83120815"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Migrar os seus dados faciais para uma subscrição face diferente

Este guia mostra-lhe como mover dados faciais, como um objeto de PersonGroup guardado com rostos, para uma subscrição diferente do Face dos Serviços Cognitivos Azure. Para mover os dados, utiliza a função Snapshot. Desta forma evita ter de construir e treinar repetidamente um objeto PersonGroup ou FaceList quando se move ou expande as suas operações. Por exemplo, talvez tenha criado um objeto PersonGroup usando uma subscrição de teste gratuito e agora quer emigrar para a sua subscrição paga. Ou pode precisar de sincronizar dados faciais em assinaturas em diferentes regiões para uma grande operação empresarial.

Esta mesma estratégia de migração também se aplica aos objetos LargePersonGroup e LargeFaceList. Se não está familiarizado com os conceitos deste guia, consulte as suas definições no guia de conceitos de [reconhecimento facial.](../concepts/face-recognition.md) Este guia utiliza a biblioteca de clientes Face .NET com C#.

## <a name="prerequisites"></a>Pré-requisitos

Precisa dos seguintes itens:

- Duas chaves de subscrição Face, uma com os dados existentes e outra para migrar para. Para subscrever o serviço Face e obter a sua chave, siga as instruções na [conta Criar uma conta de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
- A cadeia de ID de subscrição Face que corresponde à subscrição do alvo. Para encontrá-lo, selecione **Overview** no portal Azure. 
- Qualquer edição do [Visual Studio 2015 ou 2017.](https://www.visualstudio.com/downloads/)

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

Este guia utiliza uma simples aplicação de consola para executar a migração de dados faciais. Para uma implementação completa, consulte a [amostra de snapshot face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) no GitHub.

1. No Visual Studio, crie uma nova app de consola .NET Framework project. **Nomeie-o FaceApiSnapshotSample**.
1. Obtenha os pacotes NuGet necessários. Clique no seu projeto no Solution Explorer e selecione **Gerir pacotes NuGet**. Selecione o separador **Browse** e **selecione Incluir pré-lançamento**. Encontre e instale o seguinte pacote:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Criar clientes de rosto

No método **Principal** em *Program.cs,* crie duas instâncias [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) para a sua origem e subscrições-alvo. Este exemplo utiliza uma subscrição face na região da Ásia Oriental como fonte e uma subscrição dos EUA ocidentais como alvo. Este exemplo demonstra como migrar dados de uma região do Azure para outra. 

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

```csharp
var FaceClientEastAsia = new FaceClient(new ApiKeyServiceClientCredentials("<East Asia Subscription Key>"))
    {
        Endpoint = "https://southeastasia.api.cognitive.microsoft.com/>"
    };

var FaceClientWestUS = new FaceClient(new ApiKeyServiceClientCredentials("<West US Subscription Key>"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com/"
    };
```

Preencha os valores-chave de subscrição e urLs de ponto final para as suas assinaturas de origem e alvo.


## <a name="prepare-a-persongroup-for-migration"></a>Preparar um PersonGroup para a migração

Precisa da identificação do PersonGroup na sua subscrição de origem para o migrar para a subscrição do alvo. Utilize as [Extensões persongroupOperations.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) para recuperar uma lista dos seus objetos PersonGroup. Em seguida, obtenha a propriedade [PersonGroup.PersonGroupId.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) Este processo parece diferente com base nos objetos do PersonGroup que tem. Neste guia, o ID pessoa de origem é armazenado em `personGroupId` .

> [!NOTE]
> O [código da amostra](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) cria e treina um novo PersonGroup para migrar. Na maioria dos casos, já deve ter um PersonGroup para usar.

## <a name="take-a-snapshot-of-a-persongroup"></a>Tire uma foto de um PersonGroup

Um instantâneo é armazenamento remoto temporário para certos tipos de dados face. Funciona como uma espécie de clipboard para copiar dados de uma subscrição para outra. Primeiro, tira uma foto dos dados na subscrição de origem. Em seguida, aplica-o a um novo objeto de dados na subscrição do alvo.

Utilize a instância FaceClient da subscrição de origem para tirar uma fotografia do PersonGroup. Utilize [takeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) com o ID do PersonGroup e o ID da subscrição do alvo. Se tiver várias subscrições de alvo, adicione-as como entradas de matriz no terceiro parâmetro.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> O processo de tomada e aplicação de instantâneos não perturba quaisquer chamadas regulares para a fonte ou alvo PersonGroups ou FaceLists. Não faça chamadas simultâneas que alterem o objeto de origem, como chamadas de [gestão FaceList](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) ou a chamada do [PersonGroup Train,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) por exemplo. A operação instantânea pode ser executada antes ou depois dessas operações ou pode encontrar erros.

## <a name="retrieve-the-snapshot-id"></a>Recuperar o ID instantâneo

O método usado para tirar fotografias é assíncrono, por isso deve esperar pela sua conclusão. As operações instantâneas não podem ser canceladas. Neste código, o `WaitForOperation` método monitoriza a chamada assíncrona. Verifica o estado a cada 100 ms. Depois de terminar a operação, recupere um ID de operação analisando o `OperationLocation` campo. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Um valor típico `OperationLocation` é assim:

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

O `WaitForOperation` método de ajudante está aqui:

```csharp
/// <summary>
/// Waits for the take/apply operation to complete and returns the final operation status.
/// </summary>
/// <returns>The final operation status.</returns>
private static async Task<OperationStatus> WaitForOperation(IFaceClient client, Guid operationId)
{
    OperationStatus operationStatus = null;
    do
    {
        if (operationStatus != null)
        {
            Thread.Sleep(TimeSpan.FromMilliseconds(100));
        }

        // Get the status of the operation.
        operationStatus = await client.Snapshot.GetOperationStatusAsync(operationId);

        Console.WriteLine($"Operation Status: {operationStatus.Status}");
    }
    while (operationStatus.Status != OperationStatusType.Succeeded
            && operationStatus.Status != OperationStatusType.Failed);

    return operationStatus;
}
```

Depois do estado de funcionamento mostrar `Succeeded` , obtenha o ID instantâneo analisando o campo da instância `ResourceLocation` Destantes Do Estado de Funcionamento.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Um valor típico `resourceLocation` é assim:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Aplicar um instantâneo a uma subscrição de alvo

Em seguida, crie o novo PersonGroup na subscrição do alvo utilizando um ID gerado aleatoriamente. Em seguida, utilize a instância FaceClient da subscrição do alvo para aplicar o instantâneo a este PersonGroup. Passe no ID instantâneo e no novo ID do PersonGroup.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Um objeto Snapshot é válido por apenas 48 horas. Tire apenas uma foto se pretender usá-lo para migração de dados logo a seguir.

Um pedido de aplicação de instantâneo devolve outro ID de operação. Para obter este ID, analise o `OperationLocation` campo da instância de aplicação de aplicação de voltaSnapshotResult. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

O processo de aplicação instantânea também é assíncrono, por isso, novamente, use `WaitForOperation` para esperar que termine.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Testar a migração de dados

Depois de aplicar o instantâneo, o novo PersonGroup na subscrição do alvo povoa com os dados faciais originais. Por padrão, os resultados do treino também são copiados. O novo PersonGroup está pronto para chamadas de identificação facial sem necessidade de reconversão.

Para testar a migração de dados, executar as seguintes operações e comparar os resultados que imprimem à consola:

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

Utilize os seguintes métodos de ajudante:

```csharp
private static async Task DisplayPersonGroup(IFaceClient client, string personGroupId)
{
    var personGroup = await client.PersonGroup.GetAsync(personGroupId);
    Console.WriteLine("Person Group:");
    Console.WriteLine(JsonConvert.SerializeObject(personGroup));

    // List persons.
    var persons = await client.PersonGroupPerson.ListAsync(personGroupId);

    foreach (var person in persons)
    {
        Console.WriteLine(JsonConvert.SerializeObject(person));
    }

    Console.WriteLine();
}
```

```csharp
private static async Task IdentifyInPersonGroup(IFaceClient client, string personGroupId)
{
    using (var fileStream = new FileStream("data\\PersonGroup\\Daughter\\Daughter1.jpg", FileMode.Open, FileAccess.Read))
    {
        var detectedFaces = await client.Face.DetectWithStreamAsync(fileStream);

        var result = await client.Face.IdentifyAsync(detectedFaces.Select(face => face.FaceId.Value).ToList(), personGroupId);
        Console.WriteLine("Test identify against PersonGroup");
        Console.WriteLine(JsonConvert.SerializeObject(result));
        Console.WriteLine();
    }
}
```

Agora pode utilizar o novo PersonGroup na subscrição do alvo. 

Para atualizar o target PersonGroup novamente no futuro, crie um novo PersonGroup para receber o instantâneo. Para isso, siga os passos deste guia. Um único objeto PersonGroup pode ter uma imagem instantânea aplicada apenas uma vez.

## <a name="clean-up-resources"></a>Limpar recursos

Depois de terminar os dados do rosto migratórios, elimine manualmente o objeto instantâneo.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Próximos passos

Em seguida, consulte a documentação de referência da API relevante, explore uma aplicação de amostra que utilize a funcionalidade Snapshot ou siga um guia de como começar a utilizar as outras operações da API aqui mencionadas:

- [Documentação de referência instantânea (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Amostra de instantâneo facial](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Adicionar rostos](how-to-add-faces.md)
- [Detetar rostos numa imagem](HowtoDetectFacesinImage.md)
- [Identificar rostos numa imagem](HowtoIdentifyFacesinImage.md)
