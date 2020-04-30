---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 04/14/2020
ms.openlocfilehash: cf25d69a1f3765613d0d19af8f8f1489d89db1ba
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82134104"
---
Este artigo mostra-lhe como começar a usar o Custom Vision SDK com Java para construir um modelo de deteção de objetos. Depois de criado, pode adicionar regiões marcadas, carregar imagens, treinar o projeto, obter o URL final de previsão padrão do projeto, e usar o ponto final para testar programáticamente uma imagem. Utilize este exemplo como um modelo para compilar a sua aplicação de Java.

## <a name="prerequisites"></a>Pré-requisitos

- Um IDE Java à sua escolha
- [JDK 7 ou 8](https://aka.ms/azure-jdks) instalados.
- [Maven](https://maven.apache.org/) instalado
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Obtenha o SDK de Visão Personalizada e o código da amostra

Para escrever uma aplicação de Java que utilize a Visão Personalizada, precisa dos pacotes maven da Visão Personalizada. Estes pacotes estão incluídos no projeto de amostra que você vai descarregar, mas você pode acessá-los individualmente aqui.

Você pode encontrar o Custom Vision SDK no repositório central maven:
- [SDK de Preparação](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [SDK de Predição](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Clone ou transfira o projeto [Cognitive Services Java SDK Samples](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master) (Exemplos do SDK dos Serviços Cognitivos para Java). Navegue para a pasta **Vision/CustomVision/**.

Este projeto de Java cria um projeto novo de deteção de objetos da Visão Personalizada denominado __Sample Java OD Project__, que pode ser acedido a partir do [site da Visão Personalizada](https://customvision.ai/). Em seguida, a aplicação carrega imagens para preparar e testar um classificador. Neste projeto, o classificador destina-se a determinar se um objeto é um **garfo** ou **uma tesoura.**

[!INCLUDE [get-keys](../../includes/get-keys.md)]

O programa está configurado para referenciar os seus dados-chave como variáveis ambientais. Navegue para a pasta **Vision/CustomVision** e introduza os seguintes comandos PowerShell para definir as variáveis ambientais. 

> [!NOTE]
> Se estiver a utilizar um sistema operativo não Windows, consulte [as variáveis](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication) ambiente configure para obter instruções.

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>Compreender o código

Carregue o projeto `Vision/CustomVision` no IDE Java e abra o ficheiro _CustomVisionSamples.java_. Encontre o método **runSample** e comente o método **ImageClassification_Sample** chamada&mdash;este método executa o cenário de classificação da imagem, que não está coberto por este guia. O método **ObjectDetection_Sample** implementa a funcionalidade principal deste início rápido; navegue para a respetiva definição e inspecione o código. 

### <a name="create-a-new-custom-vision-service-project"></a>Criar um novo projeto de Serviço de Visão Personalizada

Vá para o bloco de código que cria um cliente de preparação e um projeto de deteção de objetos. O projeto criado aparece no [site da Visão Personalizada](https://customvision.ai/) ao qual acedeu anteriormente. Consulte o método [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) sobrecarrega-se para especificar outras opções quando criar o seu projeto (explicado no guia do portal do [detetor).](../../get-started-build-detector.md)

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

### <a name="add-tags-to-your-project"></a>Adicionar etiquetas ao seu projeto

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

### <a name="upload-and-tag-images"></a>Enviar e marcar imagens

Quando marca imagens em projetos de deteção de objetos, é necessário especificar a região de cada objeto marcado utilizando coordenadas normalizadas. Vá para a definição do Mapa `regionMap`. Este código associa cada uma das imagens de exemplo à sua região marcada.

> [!NOTE]
> Se não tiver um utilitário de clique e arrastar para marcar as coordenadas das regiões, pode utilizar a UI web em [Customvision.ai](https://www.customvision.ai/). Neste exemplo, as coordenadas já estão fornecidas.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

Em seguida, avance para o bloco de código que adiciona as imagens ao projeto. As imagens são lidas a partir da pasta **src/main/resources** do projeto e são carregadas para o serviço com as respetivas etiquetas adequadas e as coordenadas de região.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

O fragmento de código anterior utiliza duas funções de ajudante que recuperam as imagens como fluxos de recursos e as enviam para o serviço (pode carregar até 64 imagens num único lote).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

### <a name="train-the-project-and-publish"></a>Treine o projeto e publique

Este código cria a primeira iteração do modelo de previsão e, em seguida, publica essa iteração para o ponto final da previsão. O nome dado à iteração publicada pode ser usado para enviar pedidos de previsão. Uma iteração não está disponível no ponto final da previsão até ser publicada.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]

### <a name="use-the-prediction-endpoint"></a>Use o ponto final da previsão

O ponto final de predição, representado pelo objeto `predictor` aqui, é a referência que vai servir para submeter uma imagem para o modelo atual e obter uma predição de classificação. Neste exemplo, `predictor` é definido noutro sítio através da utilização da variável de ambiente da chave de predição.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_prediction_od)]

## <a name="run-the-application"></a>Executar a aplicação

Para compilar e executar a solução utilizando o maven, navegue para o diretório do projeto **(Vision/CustomVision**) num pedido de comando e execute o comando de execução:

```bash
mvn compile exec:java
```

Veja o resultado da consola para o registo e predição de resultados. Pode, em seguida, certificar-se de que a imagem de teste está identificada de forma adequada e que a região de deteção está correta.

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Passos seguintes

Agora já viste como cada passo do processo de deteção de objetos pode ser feito em código. Esta amostra executa uma única iteração de treino, mas muitas vezes você precisará treinar e testar o seu modelo várias vezes para torná-lo mais preciso. O seguinte guia de treino trata da classificação da imagem, mas os seus princípios são semelhantes à deteção de objetos.

> [!div class="nextstepaction"]
> [Test and retrain a model](../../test-your-model.md) (Testar e voltar a preparar um modelo)
