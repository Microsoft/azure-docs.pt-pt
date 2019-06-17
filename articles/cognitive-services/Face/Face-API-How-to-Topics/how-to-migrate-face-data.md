---
title: Migre os seus dados de rostos em várias subscrições - Face API
titleSuffix: Azure Cognitive Services
description: Este guia mostra como migrar os dados de rostos armazenados de uma subscrição de Face API para outro.
services: cognitive-services
author: lewlu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: lewlu
ms.openlocfilehash: 702aed12860c090e83b997e6b56d56e06b416568
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65913803"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Migre os seus dados de rostos para uma subscrição diferente de rostos

Este guia mostra como mover dados de rostos, como um objeto de PersonGroup guardado com rostos, para uma subscrição diferente da API de Face de serviços cognitivos do Azure. Para mover os dados, utilize a funcionalidade de instantâneo. Dessa maneira é evitar ter de criar e formar um objeto PersonGroup ou FaceList quando move ou expandir as suas operações repetidamente. Por exemplo, talvez criou um objeto de PersonGroup através de uma subscrição de avaliação gratuita e agora quero migrá-la para a subscrição paga. Ou poderá ter de sincronizar os dados de rostos em várias regiões para uma operação de empresas de grande porte.

Esta estratégia de migração mesmo também se aplica a objetos LargePersonGroup e LargeFaceList. Se não estiver familiarizado com os conceitos neste guia, consulte as respetivas definições no [conceitos de reconhecimento de rostos](../concepts/face-recognition.md) guia. Este guia utiliza a biblioteca de cliente .NET de API de rostos com C#.

## <a name="prerequisites"></a>Pré-requisitos

Precisa dos seguintes itens:

- Dois Face API chaves de subscrição, um com os dados existentes e outro para migrar para o. Para subscrever o serviço de API de rostos e obtenha a chave, siga as instruções em [criar uma conta dos serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
- A cadeia de ID de subscrição do Face API corresponde à subscrição de destino. Para encontrá-lo, selecione **descrição geral** no portal do Azure. 
- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

Este guia utiliza uma aplicação de consola simples para executar a migração de dados de rostos. Para uma implementação completa, consulte a [exemplo de instantâneo da Face API](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) no GitHub.

1. No Visual Studio, crie um novo projeto de .NET Framework de aplicação de consola. Atribua o nome **FaceApiSnapshotSample**.
1. Obtenha os pacotes NuGet necessários. Com o botão direito do rato no Explorador de soluções e selecione **gerir pacotes NuGet**. Selecione o **navegue** separador e selecione **incluir pré-lançamento**. Localizar e instalar o pacote seguinte:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Criar clientes de rostos

Na **Main** método na *Program.cs*, criar dois [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) instâncias das suas subscrições de origem e de destino. Este exemplo utiliza uma subscrição de rosto na região Ásia Oriental como a origem e de uma subscrição de EUA Oeste como destino. Este exemplo demonstra como migrar dados de uma região do Azure para outra. Se as suas subscrições estão em diferentes regiões, altere o `Endpoint` cadeias de caracteres.

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

Preencha os valores de chave de subscrição e os URLs de ponto final para as suas subscrições de origem e de destino.


## <a name="prepare-a-persongroup-for-migration"></a>Preparar um PersonGroup para migração

Precisa do ID do PersonGroup na sua subscrição de origem para migrá-la para a subscrição de destino. Utilize o [PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) método para recuperar uma lista de seus objetos de PersonGroup. Em seguida, obter o [PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) propriedade. Esse processo parece diferente com base no que PersonGroup tiver os objetos. Neste guia, a origem PersonGroup ID é armazenada em `personGroupId`.

> [!NOTE]
> O [código de exemplo](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) cria e prepara um novo PersonGroup para migrar. Na maioria dos casos, já deverá ter um PersonGroup a utilizar.

## <a name="take-a-snapshot-of-a-persongroup"></a>Tirar um instantâneo de um PersonGroup

Um instantâneo é um armazenamento remoto temporário para determinados tipos de dados de rostos. Ele funciona como um tipo de área de transferência para copiar dados de uma subscrição para outro. Em primeiro lugar, tirar um instantâneo dos dados na subscrição de origem. Em seguida, aplicá-la para um novo objeto de dados na subscrição de destino.

Utilize a instância de FaceClient da subscrição de origem para tirar um instantâneo a PersonGroup. Uso [TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) com o ID de PersonGroup e ID de. subscrição de destino Se tiver várias subscrições de destino, adicioná-los como entradas de matriz no terceiro parâmetro.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> O processo de tirar e aplicar os instantâneos não interromper qualquer chamada regular para a origem ou destino PersonGroups ou FaceLists. Não faça chamadas simultâneas que altere o objeto de origem, como [chamadas de gestão de FaceList](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) ou o [PersonGroup Train](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) chamar, por exemplo. A operação de instantâneo pode ser executado antes ou depois dessas operações ou poderá encontrar erros.

## <a name="retrieve-the-snapshot-id"></a>Obter o ID de instantâneo

O método utilizado para criar instantâneos é assíncrono, pelo que terá de aguardar pela conclusão. Não não possível cancelar operações de instantâneo. Nesse código, o `WaitForOperation` método monitoriza a chamada assíncrona. Ele verifica o estado de todos os 100 ms. Depois de concluída a operação, obter um ID de operação ao analisar o `OperationLocation` campo. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Um típico `OperationLocation` valor é semelhante a este:

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

O `WaitForOperation` método auxiliar está aqui:

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

Depois do Estado da operação mostra `Succeeded`, obtenha o ID do instantâneo ao analisar o `ResourceLocation` campo da instância OperationStatus retornada.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Um típico `resourceLocation` valor é semelhante a este:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Aplicar um instantâneo para uma subscrição de destino

Em seguida, crie o novo PersonGroup na subscrição de destino com um ID gerado aleatoriamente. Em seguida, utilize a instância de FaceClient a subscrição de destino para aplicar o instantâneo para esta PersonGroup. Passar o instantâneo ID e o novo ID PersonGroup.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Um objeto de instantâneo é válido durante apenas 48 horas. Apenas tirar um instantâneo se pretende usá-lo para a migração de dados logo após.

Um pedido de aplicar instantâneo retorna a ID do ambiente de trabalho outra operação. Para obter este ID, analisar o `OperationLocation` campo da instância applySnapshotResult retornado. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

O processo de instantâneo do aplicativo também é assíncrono, utilize novamente `WaitForOperation` aguardar sua finalização.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>A migração de dados de teste

Depois de aplicar o instantâneo, o novo PersonGroup na subscrição de destino será preenchido com os dados originais de rostos. Por predefinição, os resultados de treinamento também são copiados. O novo PersonGroup está pronta para chamadas de identificação de face sem a necessidade de reparametrização.

Para testar a migração de dados, execute as seguintes operações e comparar os resultados que eles imprimam para a consola:

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

Utilize os seguintes métodos de programa auxiliar:

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

Agora, pode utilizar o novo PersonGroup na subscrição de destino. 

Para atualizar o destino PersonGroup novamente no futuro, crie um novo PersonGroup para receber o instantâneo. Para tal, siga os passos neste guia. Um único objeto PersonGroup pode ter um instantâneo aplicado apenas uma vez.

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir a migração de dados de rostos, elimine manualmente o objeto de instantâneo.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Passos Seguintes

Em seguida, veja a relevante documentação de referência de API, explorar uma aplicação de exemplo que utiliza a funcionalidade de instantâneo ou seguir um guia de procedimentos para começar a utilizar as outras operações de API mencionadas aqui:

- [Documentação de referência do instantâneo (SDK do .NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Exemplo de instantâneo da API face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Adicionar rostos](how-to-add-faces.md)
- [Detetar rostos numa imagem](HowtoDetectFacesinImage.md)
- [Identificar rostos numa imagem](HowtoIdentifyFacesinImage.md)
