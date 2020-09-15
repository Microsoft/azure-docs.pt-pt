---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 08/17/2020
ms.openlocfilehash: 8af5d8ad2ed4d623d7128ffa5fba3c9e3647a057
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90533278"
---
Este artigo mostra-lhe como começar a usar a biblioteca de clientes Custom Vision com C# para construir um modelo de deteção de objetos. Depois de criado, pode adicionar regiões marcadas, carregar imagens, treinar o projeto, obter o URL final de previsão padrão do projeto, e usar o ponto final para testar programáticamente uma imagem. Use este exemplo como um modelo para construir a sua própria aplicação .NET. 

## <a name="prerequisites"></a>Pré-requisitos

- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/)
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="get-the-custom-vision-client-library-and-sample-code"></a>Obtenha a biblioteca de clientes Visão Personalizada e o código de amostra

Para escrever uma aplicação .NET que utiliza visão personalizada, vai precisar dos pacotes Custom Vision NuGet. Estes pacotes estão incluídos no projeto de amostra que você vai descarregar, mas você pode acessá-los individualmente aqui.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Clone ou transfira o projeto [Exemplos de .NET dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Navegue para a pasta **CustomVision/ObjectDetection** e abra _ObjectDetection.csproj_ em Estúdio Visual.

Este projeto do Visual Studio cria um projeto de Visão Personalizada novo denominado __My New Project__, que está acessível através do [site da Visão Personalizada](https://customvision.ai/). Em seguida, o projeto carrega imagens para preparar e testar um modelo de deteção de objetos. Neste projeto, o modelo está preparado para detetar garfos e tesouras nas imagens.

[!INCLUDE [get-keys](../../includes/get-keys.md)]

## <a name="examine-the-code"></a>Examinar o código

Abra o ficheiro _Program.cs_ e inspecione o código. [Crie variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para as suas teclas de treino e previsão `CUSTOM_VISION_TRAINING_KEY` nomeadas `CUSTOM_VISION_PREDICTION_KEY` e, respectivamente. O guião procurará estas variáveis.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_keys)]

Além disso, obtenha o url do Ponto Final na página de Definições do website Da Visão Personalizada. Guarde-o para uma variável ambiental chamada `CUSTOM_VISION_ENDPOINT`. O guião guarda uma referência a ele na raiz da sua classe.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_endpoint)]

## <a name="create-a-new-custom-vision-service-project"></a>Criar um novo projeto de Serviço de Visão Personalizada

A parte de código seguinte cria um projeto de deteção de objetos. O projeto criado aparece no [site da Visão Personalizada](https://customvision.ai/) ao qual acedeu anteriormente. Consulte o método [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__&preserve-view=true) para especificar outras opções quando criar o seu projeto (explicado no guia do portal web [do detetor).](../../get-started-build-detector.md)  

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>Adicione tags ao projeto

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>Enviar e marcar imagens

Quando marca imagens em projetos de deteção de objetos, é necessário especificar a região de cada objeto marcado utilizando coordenadas normalizadas. O código seguinte associa cada uma das imagens da amostra à sua região marcada.

> [!NOTE]
> Se não tiver um utilitário de clique e arrasto para marcar as coordenadas das regiões, pode utilizar a UI web em [Customvision.ai](https://www.customvision.ai/). Neste exemplo, as coordenadas já estão fornecidas.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

Depois, utilize este mapa de associações para carregar cada imagem de exemplo com as respetivas coordenadas de região. Pode carregar até 64 imagens num único lote.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

Neste ponto, você carregou todas as amostras imagens e marcou cada uma **(garfo** ou **tesoura**) com um retângulo de pixel associado.

## <a name="train-the-project"></a>Treine o projeto

Este código cria a primeira iteração de treino no projeto.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

> [!TIP]
> Comboio com etiquetas selecionadas
>
> Pode treinar opcionalmente apenas um subconjunto das suas etiquetas aplicadas. Podes querer fazer isto se ainda não aplicaste o suficiente de certas etiquetas, mas tens o suficiente de outras. Na chamada [TrainProject,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.trainproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_TrainProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_Guid_System_String_System_Nullable_System_Int32__System_Nullable_System_Boolean__System_String_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_Models_TrainingParameters_&preserve-view=true) utilize o parâmetro *de treino Parameters.* Construa um [TrainingParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.models.trainingparameters?view=azure-dotnet&preserve-view=true) e coloque a sua propriedade **SelectedTags** numa lista de IDs das etiquetas que pretende utilizar. O modelo treinará para reconhecer apenas as etiquetas dessa lista.

## <a name="publish-the-current-iteration"></a>Publique a iteração atual

O nome dado à iteração publicada pode ser usado para enviar pedidos de previsão. Uma iteração não está disponível no ponto final da previsão até ser publicada.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]

## <a name="create-a-prediction-endpoint"></a>Criar um ponto final de previsão

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction_endpoint)]

## <a name="use-the-prediction-endpoint"></a>Use o ponto final da previsão

Esta parte do script carrega a imagem de teste, consulta o ponto final do modelo e produz dados de previsão para a consola.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Executar a aplicação

À medida que a aplicação corre, deve abrir uma janela da consola e escrever a seguinte saída:

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

Pode então verificar se a imagem de teste (encontrada em **Imagens/Teste/** ) está devidamente marcada e que a região de deteção está correta. Neste ponto, pode premir qualquer tecla para sair da aplicação.

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Passos seguintes

Agora já viste como fazer todos os passos do processo de deteção de objetos em código. Esta amostra executa uma única iteração de treino, mas muitas vezes você precisa treinar e testar o seu modelo várias vezes para torná-lo mais preciso. O guia seguinte aborda a classificação de imagens, mas os seus princípios são semelhantes à deteção de objetos.

> [!div class="nextstepaction"]
> [Test and retrain a model](../../test-your-model.md) (Testar e voltar a preparar um modelo)
