---
author: areddish
ms.custom: devx-track-java
ms.author: areddish
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: f4b64c25bff93683c79aa1aa3eb556988c798cb0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "90604995"
---
Este guia fornece instruções e código de amostra para ajudá-lo a começar a usar a biblioteca de clientes Da Visão Personalizada para a Java para construir um modelo de classificação de imagem. Você vai criar um projeto, adicionar tags, treinar o projeto, e usar o URL de previsão do projeto para testá-lo programáticamente. Use este exemplo como um modelo para construir a sua própria app de reconhecimento de imagem.

> [!NOTE]
> Se quiser construir e treinar um modelo de classificação _sem_ escrever código, consulte a [orientação baseada no navegador.](../../getting-started-build-a-classifier.md)

## <a name="prerequisites"></a>Pré-requisitos

- Um IDE Java à sua escolha
- [JDK 7 ou 8](https://aka.ms/azure-jdks) instalados.
- [Maven](https://maven.apache.org/) instalado
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="get-the-custom-vision-client-library"></a>Obtenha a biblioteca de clientes Da Visão Personalizada

Para escrever uma aplicação de análise de imagem com visão personalizada para Java, você precisará dos pacotes maven Da Visão Personalizada. Estes pacotes estão incluídos no projeto de amostra que você vai descarregar, mas você pode acessá-los individualmente aqui.

Pode encontrar a biblioteca de clientes Da Visão Personalizada no repositório central maven:

- [SDK de Preparação](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [SDK de Predição](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Clone ou transfira o projeto [Cognitive Services Java SDK Samples](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master) (Exemplos do SDK dos Serviços Cognitivos para Java). Navegue para a pasta **Vision/CustomVision/**.

Este projeto de Java cria um projeto novo de classificação de imagens da Visão Personalizada denominado __Sample Java Project__, que pode ser acedido a partir do [site da Visão Personalizada](https://customvision.ai/). Em seguida, a aplicação carrega imagens para preparar e testar um classificador. Neste projeto, o classificador tem como objetivo determinar se uma árvore é uma __cicuta__ ou uma __cerejeira japonesa__.

[!INCLUDE [get-keys](../../includes/get-keys.md)]

O programa está configurado para referenciar os seus dados-chave como variáveis ambientais. Navegue na pasta **Vision/CustomVision** e introduza os seguintes comandos PowerShell para definir as variáveis ambientais. 

> [!NOTE]
> Se estiver a utilizar um sistema operativo não-Windows, consulte [variáveis ambientais configure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication) para obter instruções.

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="examine-the-code"></a>Examinar o código

Carregue o projeto `Vision/CustomVision` no IDE Java e abra o ficheiro _CustomVisionSamples.java_. Encontre o método **runSample** e comente o método **ObjectDetection_Sample** chamada &mdash; este método executa o cenário de deteção de objetos, que não está coberto neste guia. O método **ImageClassification_Sample** implementa a funcionalidade principal deste exemplo; navegue para a respetiva definição e inspecione o código.

## <a name="create-a-custom-vision-project"></a>Criar um projeto de Visão Personalizada

A primeira parte do código cria um projeto de classificação de imagens. O projeto criado aparece no [site da Visão Personalizada](https://customvision.ai/) ao qual acedeu anteriormente. Consulte o método [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) sobrecarrega para especificar outras opções quando criar o seu projeto (explicado no guia do portal web [do classificador).](../../getting-started-build-a-classifier.md)

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create)]

## <a name="create-tags-in-the-project"></a>Criar etiquetas no projeto

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>Enviar e marcar imagens

As imagens de exemplo estão incluídas na pasta **src/main/resources** do projeto. São lidas a partir daí e carregadas para o serviço com as etiquetas adequadas.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload)]

O corte de código anterior utiliza duas funções de ajudante que recuperam as imagens como fluxos de recursos e as enviam para o serviço (pode carregar até 64 imagens num único lote).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

## <a name="train-and-publish-the-project"></a>Treine e publique o projeto

Este código cria a primeira iteração do modelo de previsão e, em seguida, publica essa iteração para o ponto final de previsão. O nome dado à iteração publicada pode ser usado para enviar pedidos de previsão. Uma iteração não está disponível no ponto final da previsão até que seja publicada.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train)]

## <a name="use-the-prediction-endpoint"></a>Use o ponto final da previsão

O ponto final de predição, representado pelo objeto `predictor` aqui, é a referência que vai servir para submeter uma imagem para o modelo atual e obter uma predição de classificação. Neste exemplo, `predictor` é definido noutro sítio através da utilização da variável de ambiente da chave de predição.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict)]

## <a name="run-the-application"></a>Executar a aplicação

Para compilar e executar a solução utilizando maven, navegue para o diretório do projeto **(Vision/CustomVision)** num pedido de comando e execute o comando de execução:

```bash
mvn compile exec:java
```

A saída da consola da aplicação deve ser semelhante ao seguinte texto:

```console
Creating project...
Adding images...
Adding image: hemlock_1.jpg
Adding image: hemlock_2.jpg
Adding image: hemlock_3.jpg
Adding image: hemlock_4.jpg
Adding image: hemlock_5.jpg
Adding image: hemlock_6.jpg
Adding image: hemlock_7.jpg
Adding image: hemlock_8.jpg
Adding image: hemlock_9.jpg
Adding image: hemlock_10.jpg
Adding image: japanese_cherry_1.jpg
Adding image: japanese_cherry_2.jpg
Adding image: japanese_cherry_3.jpg
Adding image: japanese_cherry_4.jpg
Adding image: japanese_cherry_5.jpg
Adding image: japanese_cherry_6.jpg
Adding image: japanese_cherry_7.jpg
Adding image: japanese_cherry_8.jpg
Adding image: japanese_cherry_9.jpg
Adding image: japanese_cherry_10.jpg
Training...
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Agora, pode verificar que a predição da imagem de teste (as últimas linhas da saída) está correta.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Passos seguintes

Agora já viste como cada passo do processo de deteção de objetos pode ser feito em código. Esta amostra executa uma única iteração de treino, mas muitas vezes você precisa treinar e testar o seu modelo várias vezes para torná-lo mais preciso.

> [!div class="nextstepaction"]
> [Test and retrain a model](../../test-your-model.md) (Testar e voltar a preparar um modelo)

* [O que é a Visão Personalizada?](../../overview.md)
* * [Documentação de referência da SDK](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/customvision?view=azure-java-stable)