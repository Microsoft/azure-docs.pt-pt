---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 09/15/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 28bcaa898bbf6621295bc5096a924d39073e727e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "90605033"
---
Este guia fornece instruções e código de amostra para ajudá-lo a começar a usar a biblioteca de clientes Custom Vision para C# para construir um modelo de classificação de imagem. Você vai criar um projeto, adicionar tags, treinar o projeto, e usar o URL de previsão do projeto para testá-lo programáticamente. Use este exemplo como um modelo para construir a sua própria app de reconhecimento de imagem.

> [!NOTE]
> Se quiser construir e treinar um modelo de classificação _sem_ escrever código, consulte a [orientação baseada no navegador.](../../getting-started-build-a-classifier.md)

## <a name="prerequisites"></a>Pré-requisitos

- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/)
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="install-the-custom-vision-client-library"></a>Instale a biblioteca de clientes Custom Vision

Para escrever uma aplicação de análise de imagem com visão personalizada para .NET, você precisará dos pacotes Custom Vision NuGet. Estes pacotes estão incluídos no projeto de amostra que você vai descarregar, mas você pode acessá-los individualmente aqui.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Clone ou transfira o projeto [Exemplos de .NET dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Navegue para a pasta **CustomVision/ImageClassification** e abra _ImageClassification.csproj_ no Visual Studio.

Este projeto do Visual Studio cria um projeto de Visão Personalizada novo denominado __My New Project__, que está acessível através do [site da Visão Personalizada](https://customvision.ai/). Em seguida, a aplicação carrega imagens para preparar e testar um classificador. Neste projeto, o classificador tem como objetivo determinar se uma árvore é uma __cicuta__ ou uma __cerejeira japonesa__.

[!INCLUDE [get-keys](../../includes/get-keys.md)]

## <a name="examine-the-code"></a>Examinar o código

Abra o ficheiro _Program.cs_ e inspecione o código. [Crie variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para as suas teclas de treino e previsão `CUSTOM_VISION_TRAINING_KEY` nomeadas `CUSTOM_VISION_PREDICTION_KEY` e, respectivamente. O guião procurará estas variáveis.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_keys)]

Além disso, obtenha o url do Ponto Final na página de Definições do website Da Visão Personalizada. Guarde-o para uma variável ambiental chamada `CUSTOM_VISION_ENDPOINT`. O guião guarda uma referência a ele na raiz da sua classe.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_endpoint)]

As linhas de código seguintes executam a funcionalidade principal do projeto.

## <a name="create-a-new-custom-vision-service-project"></a>Criar um novo projeto do Serviço de Visão Personalizada

O projeto criado aparece no [site da Visão Personalizada](https://customvision.ai/) ao qual acedeu anteriormente. Consulte o método [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) para especificar outras opções quando criar o seu projeto (explicado no guia do portal do [web classificador).](../../getting-started-build-a-classifier.md)   

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_create)]

## <a name="create-tags-in-the-project"></a>Criar etiquetas no projeto

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>Enviar e marcar imagens

As imagens para este projeto estão incluídas. São referenciadas no método **LoadImagesFromDisk** em _Program.cs_. Pode carregar até 64 imagens num único lote.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]

## <a name="train-the-classifier-and-publish"></a>Treine o classificador e publique

Este código cria a primeira iteração do modelo de previsão e, em seguida, publica essa iteração para o ponto final de previsão. Pode usar o nome da iteração para enviar pedidos de previsão. Uma iteração não está disponível no ponto final da previsão até ser publicada.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

## <a name="set-the-prediction-endpoint"></a>Definir o ponto final de predição

O ponto final de predição é a referência que vai servir para submeter uma imagem para o modelo atual e obter uma predição de classificação.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction_endpoint)]

## <a name="test-the-prediction-endpoint"></a>Teste o ponto final de previsão

Neste script, a imagem de teste é carregada no método **LoadImagesFromDisk** e a saída da predição do modelo vai ser apresentada na consola. O valor da `publishedModelName` variável deve corresponder ao valor "Publicado como" encontrado no separador **Performance** do site da Visão Personalizada. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Executar a aplicação

À medida que a aplicação corre, deve abrir uma janela da consola e escrever a seguinte saída:

```console
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

Depois, pode confirmar se a imagem de teste (que se encontra em **Images/Test/**) está etiquetada adequadamente. Prima qualquer tecla para sair da aplicação. Também pode regressar ao [site da Visão Personalizada](https://customvision.ai) e ver o estado atual do projeto criado recentemente.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Passos seguintes

Agora já deu todos os passos do processo de classificação de imagem em código. Esta amostra executa uma única iteração de treino, mas muitas vezes você precisa treinar e testar o seu modelo várias vezes para torná-lo mais preciso.

> [!div class="nextstepaction"]
> [Test and retrain a model](../../test-your-model.md) (Testar e voltar a preparar um modelo)

* [O que é a Visão Personalizada?](../../overview.md)
* [Documentação de referência da SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/customvision?view=azure-dotnet)