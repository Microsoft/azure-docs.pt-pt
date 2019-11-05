---
title: 'Início Rápido: Criar um projeto de classificação de imagens com o SDK da Visão Personalizada para C#'
titleSuffix: Azure Cognitive Services
description: Crie um projeto, adicione etiquetas, carregue imagens, prepare o seu projeto e faça uma predição com o SDK .NET com C#.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: anroth
ms.openlocfilehash: ca21bbd77b269e3034fd69cc4685311e91295f36
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "73519107"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-net-sdk"></a>Início Rápido: Criar um projeto de classificação de imagens com o SDK .NET da Visão Personalizada

Este artigo disponibiliza informações e código de exemplo para o ajudar a começar a utilizar o SDK da Visão Personalizada com C# e compilar um modelo de classificação de imagens. Depois de criado, poderá adicionar etiquetas, carregar imagens, preparar o projeto, obter o URL de ponto final de predição predefinido do projeto e utilizar o ponto final para testar uma imagem de forma programática. Utilize este exemplo como um modelo para compilar a sua aplicação .NET. Se quiser percorrer o processo de compilar e utilizar um modelo de classificação _sem_ recorrer a código, veja antes as [orientações baseadas no browser](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Pré-requisitos

- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/)
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Obter o SDK de Visão Personalizada e o exemplo de código

Para escrever uma aplicação .NET que utilize a Visão Personalizada, precisa dos pacotes NuGet da Visão Personalizada. Esses pacotes são incluídos no projeto de exemplo que você vai baixar, mas você pode acessá-los individualmente aqui.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Clone ou transfira o projeto [Exemplos de .NET dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Navegue para a pasta **CustomVision/ImageClassification** e abra _ImageClassification.csproj_ no Visual Studio.

Este projeto do Visual Studio cria um projeto de Visão Personalizada novo denominado __My New Project__, que está acessível através do [site da Visão Personalizada](https://customvision.ai/). Em seguida, a aplicação carrega imagens para preparar e testar um classificador. Neste projeto, o classificador tem como objetivo determinar se uma árvore é uma __cicuta__ ou uma __cerejeira japonesa__.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Compreender o código

Abra o ficheiro _Program.cs_ e inspecione o código. [Crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para suas chaves de treinamento e previsão nomeadas `CUSTOM_VISION_TRAINING_KEY` e `CUSTOM_VISION_PREDICTION_KEY`, respectivamente. O script irá procurar por eles.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_keys)]

Além disso, obtenha a URL do ponto de extremidade na página Configurações do site Visão Personalizada. Salve-o em uma variável de ambiente chamada `CUSTOM_VISION_ENDPOINT`. O script salva uma referência a ele na raiz da sua classe.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_endpoint)]

As linhas de código seguintes executam a funcionalidade principal do projeto.

### <a name="create-a-new-custom-vision-service-project"></a>Criar um novo projeto do Serviço de Visão Personalizada

O projeto criado aparece no [site da Visão Personalizada](https://customvision.ai/) ao qual acedeu anteriormente. Consulte o método [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) para especificar outras opções ao criar seu projeto (explicado no guia [criar um](getting-started-build-a-classifier.md) portal da Web do classificador).   

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>Criar etiquetas no projeto

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Carregar e etiquetar imagens

As imagens para este projeto estão incluídas. São referenciadas no método **LoadImagesFromDisk** em _Program.cs_. Você pode carregar até 64 imagens em um único lote.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]

### <a name="train-the-classifier-and-publish"></a>Treinar o classificador e publicar

Esse código cria a primeira iteração no projeto e, em seguida, publica essa iteração no ponto de extremidade de previsão. O nome fornecido para a iteração publicada pode ser usado para enviar solicitações de previsão. Uma iteração não está disponível no ponto de extremidade de previsão até que seja publicada.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

### <a name="set-the-prediction-endpoint"></a>Definir o ponto final de predição

O ponto final de predição é a referência que vai servir para submeter uma imagem para o modelo atual e obter uma predição de classificação.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction_endpoint)]

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>Submeter uma imagem para o ponto final de predição predefinido

Neste script, a imagem de teste é carregada no método **LoadImagesFromDisk** e a saída da predição do modelo vai ser apresentada na consola. O valor da variável publishedModelName deve corresponder ao valor "publicado como" encontrado na guia **desempenho** do portal visão personalizada. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction)]

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

## <a name="next-steps"></a>Passos seguintes

Viu como cada passo do processo de classificação de imagens pode ser feito no código. Este exemplo executa uma iteração de preparação individual, mas, muitas vezes, terá de preparar e testar o seu modelo várias vezes para torná-lo mais preciso.

> [!div class="nextstepaction"]
> [Test and retrain a model](test-your-model.md) (Testar e voltar a preparar um modelo)
