---
title: 'Início rápido: Criar um projeto de classificação de imagem com o SDK de visão personalizada paraC#'
titlesuffix: Azure Cognitive Services
description: Crie um projeto, adicione etiquetas, carregue imagens, prepare o seu projeto e faça uma predição com o SDK .NET com C#.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: anroth
ms.openlocfilehash: 28ea62ffa7a2b163b984c089649c1cd99d5e4556
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827559"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-net-sdk"></a>Início rápido: Criar um projeto de classificação de imagem com o SDK de .NET de visão personalizada

Este artigo disponibiliza informações e código de exemplo para o ajudar a começar a utilizar o SDK da Visão Personalizada com C# e compilar um modelo de classificação de imagens. Depois de criado, poderá adicionar etiquetas, carregar imagens, preparar o projeto, obter o URL de ponto final de predição predefinido do projeto e utilizar o ponto final para testar uma imagem de forma programática. Utilize este exemplo como um modelo para compilar a sua aplicação .NET. Se quiser percorrer o processo de compilar e utilizar um modelo de classificação _sem_ recorrer a código, veja antes as [orientações baseadas no browser](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Pré-requisitos

- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/)

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Obter o SDK de Visão Personalizada e o exemplo de código

Para escrever uma aplicação .NET que utilize a Visão Personalizada, precisa dos pacotes NuGet da Visão Personalizada. Esses pacotes estão incluídos no projeto de exemplo, irá transferir, mas pode acessá-los individualmente aqui.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Clone ou transfira o projeto [Exemplos de .NET dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Navegue para a pasta **CustomVision/ImageClassification** e abra _ImageClassification.csproj_ no Visual Studio.

Este projeto do Visual Studio cria um projeto de Visão Personalizada novo denominado __My New Project__, que está acessível através do [site da Visão Personalizada](https://customvision.ai/). Em seguida, a aplicação carrega imagens para preparar e testar um classificador. Neste projeto, o classificador tem como objetivo determinar se uma árvore é uma __cicuta__ ou uma __cerejeira japonesa__.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Compreender o código

Abra o ficheiro _Program.cs_ e inspecione o código. Introduza as chaves da sua subscrição nas definições apropriadas no método **Main**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=21-30)]

O parâmetro de ponto final deve apontar para a região em que o grupo de recursos do Azure que contém os recursos de visão personalizada foi criado no. Neste exemplo, vamos supor que a região Centro-Sul e utilizar:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=14-14)]

As linhas de código seguintes executam a funcionalidade principal do projeto.

### <a name="create-a-new-custom-vision-service-project"></a>Criar um novo projeto do Serviço de Visão Personalizada

O projeto criado aparece no [site da Visão Personalizada](https://customvision.ai/) ao qual acedeu anteriormente. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=32-34)]

### <a name="create-tags-in-the-project"></a>Criar etiquetas no projeto

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=36-38)]

### <a name="upload-and-tag-images"></a>Carregar e etiquetar imagens

As imagens para este projeto estão incluídas. São referenciadas no método **LoadImagesFromDisk** em _Program.cs_.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=40-55)]

### <a name="train-the-classifier-and-publish"></a>Treinar o classificador e publicar

Este código cria a primeira iteração no projeto e, em seguida, publica iteração para o ponto final de predição. O nome para a iteração publicada pode ser utilizado para enviar pedidos de predição. Uma iteração não está disponível o ponto final de predição até que seja publicada.

```csharp
var iteration = trainingApi.TrainProject(project.Id);
// The returned iteration will be in progress, and can be queried periodically to see when it has completed
while (iteration.Status == "Training")
{
        Thread.Sleep(1000);

        // Re-query the iteration to get it's updated status
        iteration = trainingApi.GetIteration(project.Id, iteration.Id);
}

// The iteration is now trained. Publish it to the prediction end point.
var publishedModelName = "treeClassModel";
var predictionResourceId = "<target prediction resource ID>";
trainingApi.PublishIteration(project.Id, iteration.Id, publishedModelName, predictionResourceId);
Console.WriteLine("Done!\n");
```

### <a name="set-the-prediction-endpoint"></a>Definir o ponto final de predição

O ponto final de predição é a referência que vai servir para submeter uma imagem para o modelo atual e obter uma predição de classificação.

```csharp
// Create a prediction endpoint, passing in obtained prediction key
CustomVisionPredictionClient endpoint = new CustomVisionPredictionClient()
{
        ApiKey = predictionKey,
        Endpoint = SouthCentralUsEndpoint
};
```

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>Submeter uma imagem para o ponto final de predição predefinido

Neste script, a imagem de teste é carregada no método **LoadImagesFromDisk** e a saída da predição do modelo vai ser apresentada na consola.

```csharp
// Make a prediction against the new project
Console.WriteLine("Making a prediction:");
var result = endpoint.ClassifyImage(project.Id, publishedModelName, testImage);

// Loop over each prediction and write out the results
foreach (var c in result.Predictions)
{
        Console.WriteLine($"\t{c.TagName}: {c.Probability:P1}");
}
```

## <a name="run-the-application"></a>Executar a aplicação

À medida que a aplicação é executada, deverá abrir uma janela e consola e escrever a saída seguinte:

```console
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

Depois, pode confirmar se a imagem de teste (que se encontra em **Images/Test/** ) está etiquetada adequadamente. Prima qualquer tecla para sair da aplicação. Também pode regressar ao [site da Visão Personalizada](https://customvision.ai) e ver o estado atual do projeto criado recentemente.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Passos Seguintes

Viu como cada passo do processo de classificação de imagens pode ser feito no código. Este exemplo executa uma iteração de preparação individual, mas, muitas vezes, terá de preparar e testar o seu modelo várias vezes para torná-lo mais preciso.

> [!div class="nextstepaction"]
> [Test and retrain a model](test-your-model.md) (Testar e voltar a preparar um modelo)
