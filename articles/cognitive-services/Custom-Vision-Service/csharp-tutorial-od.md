---
title: 'Início rápido: Criar um projeto de detecção de objeto com o SDK do Visão Personalizada paraC#'
titleSuffix: Azure Cognitive Services
description: Crie um projeto, adicione etiquetas, carregue imagens, prepare o seu projeto e elimine objetos com o SDK .NET com C#.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: areddish
ms.openlocfilehash: a21c535597110b8dac823888ec2a8e689a9c57e8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561154"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>Início rápido: Criar um projeto de detecção de objeto com o SDK do .NET Visão Personalizada

Este artigo apresenta informações e código de exemplo para o ajudar a começar a utilizar o SDK da Visão Personalizada com C# para criar um modelo de deteção de objetos. Depois de criado, pode adicionar regiões etiquetadas, carregar imagens, preparar o projeto, obter o URL de ponto final de predição predefinido do projeto e utilizar o ponto final para testar uma imagem através de programação. Utilize este exemplo como um modelo para compilar a sua aplicação .NET. 

## <a name="prerequisites"></a>Pré-requisitos

- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/)

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Obter o SDK de Visão Personalizada e o exemplo de código

Para escrever uma aplicação .NET que utilize a Visão Personalizada, precisa dos pacotes NuGet da Visão Personalizada. Esses pacotes são incluídos no projeto de exemplo que você vai baixar, mas você pode acessá-los individualmente aqui.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Clone ou transfira o projeto [Exemplos de .NET dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Navegue até a pasta **CustomVision/** Detection e abra Detection _. csproj_ no Visual Studio.

Este projeto do Visual Studio cria um projeto de Visão Personalizada novo denominado __My New Project__, que está acessível através do [site da Visão Personalizada](https://customvision.ai/). Em seguida, o projeto carrega imagens para preparar e testar um modelo de deteção de objetos. Neste projeto, o modelo está preparado para detetar garfos e tesouras nas imagens.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Compreender o código

Abra o ficheiro _Program.cs_ e inspecione o código. Introduza as chaves da sua subscrição nas definições apropriadas no método **Main**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=18-27)]

O parâmetro de ponto de extremidade deve apontar para a região onde o grupo de recursos do Azure que contém os recursos de Visão Personalizada foi criado no. Para este exemplo, vamos supor que a região do Sul EUA Central e use:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=14-14)]

### <a name="create-a-new-custom-vision-service-project"></a>Criar um novo projeto do Serviço de Visão Personalizada

A parte de código seguinte cria um projeto de deteção de objetos. O projeto criado aparece no [site da Visão Personalizada](https://customvision.ai/) ao qual acedeu anteriormente. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=29-35)]

### <a name="add-tags-to-the-project"></a>Adicionar etiquetas ao projeto

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=37-39)]

### <a name="upload-and-tag-images"></a>Carregar e etiquetar imagens

Ao etiquetar imagens em projetos de deteção de objeto, tem de especificar a região de cada objeto etiquetado com coordenadas normalizadas. O código seguinte associa cada uma das imagens de exemplo à respetiva região marcada.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=41-84)]

Depois, utilize este mapa de associações para carregar cada imagem de exemplo com as respetivas coordenadas de região.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=86-104)]

Nesta fase, todas as imagens de exemplo foram carregadas e todas têm uma etiqueta (**fork** [garfo] ou **scissors** [tesoura]) e um retângulo de píxel associado a essa etiqueta.

### <a name="train-the-project"></a>Preparar o projeto

Este código cria a primeira iteração de preparação no projeto.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=106-117)]

### <a name="publish-the-current-iteration"></a>Publicar a iteração atual

O nome fornecido para a iteração publicada pode ser usado para enviar solicitações de previsão. Uma iteração não está disponível no ponto de extremidade de previsão até que seja publicada.

```csharp
// The iteration is now trained. Publish it to the prediction end point.
var publishedModelName = "treeClassModel";
var predictionResourceId = "<target prediction resource ID>";
trainingApi.PublishIteration(project.Id, iteration.Id, publishedModelName, predictionResourceId);
Console.WriteLine("Done!\n");
```

### <a name="create-a-prediction-endpoint"></a>Criar um ponto final de predição

```csharp
// Create a prediction endpoint, passing in the obtained prediction key
CustomVisionPredictionClient endpoint = new CustomVisionPredictionClient()
{
        ApiKey = predictionKey,
        Endpoint = SouthCentralUsEndpoint
};
```

### <a name="use-the-prediction-endpoint"></a>Utilizar o ponto final de predição

Esta parte do script carrega a imagem de teste, consulta o ponto final do modelo e produz dados de predição na consola.

```csharp
// Make a prediction against the new project
Console.WriteLine("Making a prediction:");
var imageFile = Path.Combine("Images", "test", "test_image.jpg");
using (var stream = File.OpenRead(imageFile))
{
        var result = endpoint.DetectImage(project.Id, publishedModelName, stream);

        // Loop over each prediction and write out the results
        foreach (var c in result.Predictions)
        {
                Console.WriteLine($"\t{c.TagName}: {c.Probability:P1} [ {c.BoundingBox.Left}, {c.BoundingBox.Top}, {c.BoundingBox.Width}, {c.BoundingBox.Height} ]");
        }
}
```

## <a name="run-the-application"></a>Executar a aplicação

À medida que a aplicação é executada, deverá abrir uma janela e consola e escrever a saída seguinte:

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

Depois, pode confirmar se a imagem de teste (que está em **Images/Test/** ) está identificada corretamente e se a região de deteção é a certa. Nesta fase, pode premir qualquer tecla para sair da aplicação.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Passos Seguintes

Agora viu como cada passo do processo de deteção de objeto pode ser feito no código. Este exemplo executa uma iteração de preparação individual, mas, muitas vezes, terá de preparar e testar o seu modelo várias vezes para torná-lo mais preciso. O guia seguinte aborda a classificação de imagens, mas os seus princípios são semelhantes à deteção de objetos.

> [!div class="nextstepaction"]
> [Test and retrain a model](test-your-model.md) (Testar e voltar a preparar um modelo)
