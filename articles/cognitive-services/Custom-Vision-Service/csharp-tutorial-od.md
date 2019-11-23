---
title: 'Quickstart: Create an object detection project with the SDK for C# - Custom Vision'
titleSuffix: Azure Cognitive Services
description: Crie um projeto, adicione etiquetas, carregue imagens, prepare o seu projeto e elimine objetos com o SDK .NET com C#.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: areddish
ms.openlocfilehash: 7baa3e2de00997496edb5b445f8426d899ac65a4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383729"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>Início Rápido: criar um projeto de deteção de objetos com o SDK .NET da Visão Personalizada

Este artigo apresenta informações e código de exemplo para o ajudar a começar a utilizar o SDK da Visão Personalizada com C# para criar um modelo de deteção de objetos. Depois de criado, pode adicionar regiões etiquetadas, carregar imagens, preparar o projeto, obter o URL de ponto final de predição predefinido do projeto e utilizar o ponto final para testar uma imagem através de programação. Utilize este exemplo como um modelo para compilar a sua aplicação .NET. 

## <a name="prerequisites"></a>Pré-requisitos

- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/)
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Obter o SDK de Visão Personalizada e o exemplo de código

Para escrever uma aplicação .NET que utilize a Visão Personalizada, precisa dos pacotes NuGet da Visão Personalizada. These packages are included in the sample project you will download, but you can access them individually here.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Clone ou transfira o projeto [Exemplos de .NET dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Navigate to the **CustomVision/ObjectDetection** folder and open _ObjectDetection.csproj_ in Visual Studio.

Este projeto do Visual Studio cria um projeto de Visão Personalizada novo denominado __My New Project__, que está acessível através do [site da Visão Personalizada](https://customvision.ai/). Em seguida, o projeto carrega imagens para preparar e testar um modelo de deteção de objetos. Neste projeto, o modelo está preparado para detetar garfos e tesouras nas imagens.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Compreender o código

Abra o ficheiro _Program.cs_ e inspecione o código. [Create environment variables](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) for your training and prediction keys named `CUSTOM_VISION_TRAINING_KEY` and `CUSTOM_VISION_PREDICTION_KEY`, respectively. The script will look for these.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_keys)]

Also, get your Endpoint URL from the Settings page of the Custom Vision website. Save it to an environment variable called `CUSTOM_VISION_ENDPOINT`. The script saves a reference to it at the root of your class.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_endpoint)]

### <a name="create-a-new-custom-vision-service-project"></a>Criar um novo projeto do Serviço de Visão Personalizada

A parte de código seguinte cria um projeto de deteção de objetos. O projeto criado aparece no [site da Visão Personalizada](https://customvision.ai/) ao qual acedeu anteriormente. See the [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) method to specify other options when you create your project (explained in the [Build a detector](get-started-build-detector.md) web portal guide).  

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]


### <a name="add-tags-to-the-project"></a>Adicionar etiquetas ao projeto

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Carregar e etiquetar imagens

Ao etiquetar imagens em projetos de deteção de objeto, tem de especificar a região de cada objeto etiquetado com coordenadas normalizadas. O código seguinte associa cada uma das imagens de exemplo à respetiva região marcada.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

Depois, utilize este mapa de associações para carregar cada imagem de exemplo com as respetivas coordenadas de região. You can upload up to 64 images in a single batch.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

Nesta fase, todas as imagens de exemplo foram carregadas e todas têm uma etiqueta (**fork** [garfo] ou **scissors** [tesoura]) e um retângulo de píxel associado a essa etiqueta.

### <a name="train-the-project"></a>Preparar o projeto

Este código cria a primeira iteração de preparação no projeto.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

### <a name="publish-the-current-iteration"></a>Publish the current iteration

The name given to the published iteration can be used to send prediction requests. An iteration is not available in the prediction endpoint until it is published.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]

### <a name="create-a-prediction-endpoint"></a>Criar um ponto final de predição

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction_endpoint)]

### <a name="use-the-prediction-endpoint"></a>Utilizar o ponto final de predição

Esta parte do script carrega a imagem de teste, consulta o ponto final do modelo e produz dados de predição na consola.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

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

## <a name="next-steps"></a>Passos seguintes

Agora viu como cada passo do processo de deteção de objeto pode ser feito no código. Este exemplo executa uma iteração de preparação individual, mas, muitas vezes, terá de preparar e testar o seu modelo várias vezes para torná-lo mais preciso. O guia seguinte aborda a classificação de imagens, mas os seus princípios são semelhantes à deteção de objetos.

> [!div class="nextstepaction"]
> [Test and retrain a model](test-your-model.md) (Testar e voltar a preparar um modelo)
