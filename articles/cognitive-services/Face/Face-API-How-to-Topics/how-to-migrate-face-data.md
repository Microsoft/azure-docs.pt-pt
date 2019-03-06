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
ms.openlocfilehash: 95b339e8d7f2c5c63c30e002411152b50cece2a5
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448786"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Migre os seus dados de rostos para uma subscrição diferente de rostos

Este guia mostra como mover dados de rostos (como um guardado **PersonGroup** de faces) para uma subscrição diferente do Face API, a utilizar a funcionalidade de instantâneo. Isto permite-lhe evitar ter de criar e formar repetidamente uma **PersonGroup** ou **FaceList** quando mover ou expandir suas operações. Por exemplo, pode criar uma **PersonGroup** com uma subscrição de avaliação gratuita e agora pretende migrá-la para a subscrição paga ou poderá ter de sincronizar os dados de rostos em várias regiões para uma operação de empresas de grande porte.

Esta estratégia de migração mesmo que também se aplica aos **LargePersonGroup** e **LargeFaceList** objetos. Se não estiver familiarizado com os conceitos neste guia, consulte as respetivas definições no [glossário](../Glossary.md). Este guia utiliza a biblioteca de cliente .NET de API de rostos com C#.

## <a name="prerequisites"></a>Pré-requisitos

- Dois Face API chaves de subscrição (uma com os dados existentes e outra para migrar para o). Siga as instruções em [criar uma conta dos serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever o serviço de API de rostos e obtenha a chave.
- A cadeia de ID de subscrição de API de rostos correspondente para a subscrição de destino (encontrada no **descrição geral** painel no portal do Azure). 
- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).


## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

Este guia irá utilizar uma aplicação de consola simples para executar a migração de dados de rostos. Para uma implementação completa, consulte a [exemplo de instantâneo de API de rostos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) no GitHub.

1. No Visual Studio, crie um novo **aplicação de consola (.NET Framework)** do projeto e nomeie- **FaceApiSnapshotSample**. 
1. Obtenha os pacotes NuGet necessários. Com o botão direito no seu projeto no Solution Explorer e selecione **gerir pacotes NuGet**. Clique nas **navegue** separador e selecione **incluir pré-lançamento**; em seguida, localizar e instalar o pacote seguinte:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)


## <a name="create-face-clients"></a>Criar clientes de rostos

Na **Main** método na *Program.cs*, criar dois **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** instâncias das suas subscrições de origem e de destino. Neste exemplo, utilizamos uma subscrição de rosto na região Ásia Oriental como a origem e uma subscrição de EUA Oeste como destino. Isso demonstrará como migrar dados de uma região do Azure para outra. Se as suas subscrições estão em diferentes regiões, terá de alterar o `Endpoint` cadeias de caracteres.

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

Terá de preencher os valores de chave de subscrição e os URLs de ponto final para as suas subscrições de origem e de destino.


## <a name="prepare-a-persongroup-for-migration"></a>Preparar um PersonGroup para migração

Precisa do ID do **PersonGroup** na sua subscrição de origem para migrá-la para a subscrição de destino. Utilize o **[PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet)** método para recuperar uma lista de seu **PersonGroup** objetos; em seguida, obter o **[ PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId)** propriedade. Esse processo terá uma aparência diferente consoante o que **PersonGroup** tiver os objetos. Neste guia, a origem **PersonGroup** é armazenado numa `personGroupId`.

> [!NOTE]
> O [código de exemplo](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) cria e prepara uma nova **PersonGroup** para migrar, mas na maioria dos casos já deverá ter um **PersonGroup** a utilizar.

## <a name="take-snapshot-of-persongroup"></a>Tirar instantâneo PersonGroup

Um instantâneo é um armazenamento remoto temporário para determinados tipos de dados de rostos. Ele funciona como um tipo de área de transferência para copiar dados de uma subscrição para outro. Pela primeira vez o utilizador "usa" um instantâneo dos dados na subscrição de origem e, em seguida, eles "aplicam"-lo a um novo objeto de dados na subscrição de destino.

Utilizar a subscrição de origem **FaceClient** instância para tirar um instantâneo a **PersonGroup**, com **[TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet)** com o  **PersonGroup** ID e o ID de. subscrição de destino Se tiver várias subscrições de destino, pode adicioná-los como entradas de matriz no terceiro parâmetro.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> O processo de tirar e aplicar os instantâneos não interromperá todas as chamadas para a origem ou destino regulares **PersonGroup**s (ou **FaceList**s). No entanto, não recomendamos que faça chamadas simultâneas que alterar o objeto de origem ([chamadas de gestão da lista de rosto](https://docs.microsoft.com/rest/api/cognitiveservices/face/facelist) ou o [grupo de pessoas - Train](https://docs.microsoft.com/rest/api/cognitiveservices/face/persongroup/train) chamar, por exemplo), porque a operação de instantâneo pode executar antes ou depois dessas operações ou podem ser encontrados erros. 

## <a name="retrieve-the-snapshot-id"></a>Obter o ID de instantâneo

O instantâneo de colocar o método é assíncrono, portanto, precisará aguardar conclusão (instantâneo não não possível cancelar operações). Nesse código, o `WaitForOperation` método monitoriza a chamada assíncrona, a verificar o estado de todos os 100 MS. Quando a operação for concluída, poderá obter um ID de operação. Pode obtê-lo ao analisar o `OperationLocation` campo. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Um típico `OperationLocation` valor terá esta aparência:

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

Quando o estado da operação é marcado como `Succeeded`, em seguida, pode obter o ID do instantâneo ao analisar o `ResourceLocation` campo retornado **OperationStatus** instância.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Um típico `resourceLocation` valor terá esta aparência:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-snapshot-to-target-subscription"></a>Aplicar o instantâneo para a subscrição de destino

Em seguida, crie a nova **PersonGroup** na subscrição de destino, usando uma ID gerado aleatoriamente. Em seguida, utilize a subscrição de destino **FaceClient** instância para aplicar o instantâneo para esta PersonGroup, passando a ID do instantâneo e novos **PersonGroup** ID. 

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Um objeto de instantâneos só é válido durante 48 horas. Deverá demorar apenas um instantâneo se pretende usá-lo para a migração de dados logo após.

Um pedido de aplicar instantâneo retornará outro ID da operação. Pode obter essa ID ao analisar o `OperationLocation` campo retornado **applySnapshotResult** instância. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

O processo de instantâneo do aplicativo também é assíncrono, utilize novamente `WaitForOperation` para aguardar a conclusão da mesma.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>A migração de dados de teste

Depois de ter aplicado o instantâneo, o novo **PersonGroup** no destino subscrição deve ser preenchida com os dados originais de rostos. Por predefinição, os resultados de treinamento também são copiados, pelo que a nova **PersonGroup** estará pronto para chamadas de identificação de face sem a necessidade de reparametrização.

Para testar a migração de dados, pode executar as seguintes operações e comparar os resultados que eles imprimam para a consola.

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

Agora pode começar a utilizar a nova **PersonGroup** na subscrição de destino. 

Se deseja atualizar o destino **PersonGroup** novamente no futuro, terá de criar uma nova **PersonGroup** (seguindo os passos deste guia) para receber o instantâneo. Uma única **PersonGroup** objeto só pode ter um instantâneo aplicado a ele uma vez.

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado de migração de dados de rostos, recomendamos que as elimine manualmente o objeto de instantâneo.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="related-topics"></a>Tópicos Relacionados

- [Documentação de referência do instantâneo (SDK do .NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Exemplo de instantâneo de API de rostos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Como adicionar rostos](how-to-add-faces.md)
- [Como Detetar Rostos na Imagem](HowtoDetectFacesinImage.md)
- [Como identificar rostos na imagem](HowtoIdentifyFacesinImage.md)
