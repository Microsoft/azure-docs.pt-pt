---
title: Migrar os dados do seu rosto através de subscrições - Face
titleSuffix: Azure Cognitive Services
description: Este guia mostra-lhe como migrar os dados faciais armazenados de uma subscrição do Face para outra.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: nitinme
ms.custom: devx-track-csharp
ms.openlocfilehash: 6f6b2ed9357acf4dceeb960b1abdf6813987f657
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324897"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Migrar os dados do seu rosto para uma subscrição diferente do Face

Este guia mostra-lhe como mover dados faciais, como um objeto de PersonGroup salvo com rostos, para uma subscrição diferente do Azure Cognitive Services Face. Para mover os dados, utilize a função Snapshot. Desta forma, evita ter de construir e treinar repetidamente um objeto PersonGroup ou FaceList quando se move ou expande as suas operações. Por exemplo, talvez tenha criado um objeto PersonGroup com uma subscrição gratuita e agora queira emigrá-lo para a sua subscrição paga. Ou pode ser necessário sincronizar dados de rosto através de subscrições em diferentes regiões para uma grande operação empresarial.

Esta mesma estratégia de migração também se aplica a objetos LargePersonGroup e LargeFaceList. Se não está familiarizado com os conceitos deste guia, consulte as suas definições no guia de [conceitos de reconhecimento facial.](../concepts/face-recognition.md) Este guia utiliza a biblioteca cliente Face .NET com C#.

## <a name="prerequisites"></a>Pré-requisitos

Precisa dos seguintes itens:

- Duas teclas de subscrição Face, uma com os dados existentes e outra para migrar. Para subscrever o serviço Face e obter a sua chave, siga as instruções na [conta Criar um Serviço Cognitivo.](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)
- A cadeia de ID de subscrição Face que corresponde à subscrição-alvo. Para encontrá-lo, **selecione Overview** no portal Azure. 
- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

Este guia utiliza uma aplicação de consola simples para executar a migração de dados faciais. Para uma implementação completa, consulte a [amostra de instantâneo Face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) no GitHub.

1. No Visual Studio, crie um novo projeto de consola .NET Framework. Nomeie-o **FaceApiSnapshotSample**.
1. Obtenha os pacotes NuGet necessários. Clique com o botão direito no seu projeto no Solution Explorer e **selecione Gerir pacotes NuGet**. **Selecione** o separador Procurar e selecione **Incluir pré-relançar.** Encontre e instale o seguinte pacote:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-pré-visualização](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Criar clientes faciais

No método **Principal** em *Program.cs,* crie dois [exemplos de FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) para as suas subscrições de origem e alvo. Este exemplo utiliza uma subscrição face na região da Ásia Oriental como fonte e uma subscrição dos EUA ocidentais como alvo. Este exemplo demonstra como migrar dados de uma região de Azure para outra. 

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

Preencha os valores-chave de subscrição e URLs de ponto final para as suas subscrições de origem e destino.


## <a name="prepare-a-persongroup-for-migration"></a>Preparar um Grupo de Pessoas para a migração

Precisa do ID do PersonGroup na sua subscrição de origem para o migrar para a subscrição-alvo. Utilize o método [PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) para recuperar uma lista dos seus objetos do PersonGroup. Em seguida, obtenha a propriedade [PersonGroup.PersonGroupId.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) Este processo parece diferente com base nos objetos do PersonGroup que tem. Neste guia, o ID do PersonGroup de origem é armazenado em `personGroupId` .

> [!NOTE]
> O [código de amostra](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) cria e treina um novo PersonGroup para migrar. Na maioria dos casos, já deve ter um PersonGroup para usar.

## <a name="take-a-snapshot-of-a-persongroup"></a>Tire uma foto de um PersonGroup

Um instantâneo é armazenamento remoto temporário para certos tipos de dados face. Funciona como uma espécie de clipboard para copiar dados de uma subscrição para outra. Primeiro, tira-se uma fotografia dos dados na subscrição de origem. Em seguida, aplique-o a um novo objeto de dados na subscrição-alvo.

Utilize o exemplo faceClient da subscrição de origem para tirar uma foto do PersonGroup. Use [TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) com o ID do PersonGroup e o ID da subscrição-alvo. Se tiver várias subscrições-alvo, adicione-as como entradas de matriz no terceiro parâmetro.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> O processo de tomada e aplicação de instantâneos não perturba nenhuma chamada regular para a fonte ou target PersonGroups ou FaceLists. Não faça chamadas simultâneas que alterem o objeto de origem, como [chamadas de gestão FaceList](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) ou a chamada [do PersonGroup Train,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) por exemplo. A operação instantânea pode ser executada antes ou depois dessas operações ou pode encontrar erros.

## <a name="retrieve-the-snapshot-id"></a>Recupere a imagem instantânea

O método usado para tirar fotografias é assíncronos, por isso deve esperar pela sua conclusão. As operações fotográficas não podem ser canceladas. Neste código, o `WaitForOperation` método monitoriza a chamada assíncronea. Verifica o estado a cada 100 m. Após o fim da operação, recupere uma identificação de operação analisando o `OperationLocation` campo. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Um valor típico `OperationLocation` é o seguinte:

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

O `WaitForOperation` método do ajudante está aqui:

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

Após a demonstração do estado da `Succeeded` operação, obtenha o ID instantâneo analisando o `ResourceLocation` campo da instância OperaçãoStatus devolvida.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Um valor típico `resourceLocation` é o seguinte:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Aplique um instantâneo numa subscrição-alvo

Em seguida, crie o novo PersonGroup na subscrição-alvo utilizando um ID gerado aleatoriamente. Em seguida, utilize o exemplo faceClient da subscrição-alvo para aplicar o instantâneo a este PersonGroup. Passe no ID instantâneo e no novo ID do PersonGroup.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Um objeto Snapshot é válido por apenas 48 horas. Só tire uma foto se pretender usá-la para migração de dados logo depois.

Um pedido de aplicação instantânea devolve outro ID de operação. Para obter este ID, analise o `OperationLocation` campo do devolvido aplica-se InstânciaSnapshotResult. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

O processo de aplicação snapshot também é assíncronos, por isso, novamente, use `WaitForOperation` para esperar que termine.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Testar a migração de dados

Depois de aplicar o instantâneo, o novo PersonGroup na subscrição-alvo povoa com os dados faciais originais. Por padrão, os resultados do treino também são copiados. O novo PersonGroup está pronto para chamadas de identificação facial sem precisar de ser requalificado.

Para testar a migração de dados, executar as seguintes operações e comparar os resultados que imprimem à consola:

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

Utilize os seguintes métodos de ajuda:

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

Agora pode utilizar o novo PersonGroup na subscrição-alvo. 

Para atualizar o PersonGroup alvo novamente no futuro, crie um novo PersonGroup para receber o instantâneo. Para isso, siga os passos deste guia. Um único objeto PersonGroup pode ter uma imagem aplicada apenas uma vez.

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de terminar os dados do rosto migratório, elimine manualmente o objeto instantâneo.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Passos seguintes

Em seguida, consulte a documentação de referência da API relevante, explore uma aplicação de amostra que utiliza a funcionalidade Snapshot ou siga um guia de como começar a usar as outras operações da API aqui mencionadas:

- [Documentação de referência instantânea (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Amostra de instantâneo facial](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Adicionar rostos](how-to-add-faces.md)
- [Detetar rostos numa imagem](HowtoDetectFacesinImage.md)