---
title: 'Início rápido: Criar um projeto de deteção de objeto com o SDK de visão personalizada para Java'
titlesuffix: Azure Cognitive Services
description: Crie um projeto, adicione etiquetas, carregue imagens, prepare o seu projeto e elimine objetos com o SDK do Java.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: areddish
ms.openlocfilehash: 7e401fe099a4e18573ccfe2aacda6068c37abfb7
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58352056"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-sdk-for-java"></a>Início rápido: Criar um projeto de deteção de objeto com o SDK de visão personalizada para Java

Este artigo apresenta informações e código de exemplo para ajudá-lo a começar a utilizar o SDK de Visão Personalizada com o Java para criar um modelo de deteção de objeto. Depois de criado, pode adicionar regiões etiquetadas, carregar imagens, preparar o projeto, obter o URL de ponto final de predição predefinido do projeto e utilizar o ponto final para testar uma imagem através de programação. Utilize este exemplo como um modelo para compilar a sua aplicação de Java.

## <a name="prerequisites"></a>Pré-requisitos

- Um IDE Java à sua escolha
- [JDK 7 ou 8](https://aka.ms/azure-jdks) instalado.
- O Maven instalado

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Obter o SDK de Visão Personalizada e o exemplo de código

Para escrever uma aplicação de Java que utilize a Visão Personalizada, precisa dos pacotes maven da Visão Personalizada. Estes pacotes estão incluídos no projeto de exemplo que transferiu, mas pode aceder individualmente aos mesmos aqui.

Pode instalar o SDK de Visão Personalizada a partir do repositório central maven:
- [SDK de Preparação](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [SDK de Predição](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Clone ou transfira o projeto [Cognitive Services Java SDK Samples](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master) (Exemplos do SDK dos Serviços Cognitivos para Java). Navegue para a pasta **Vision/CustomVision/**.

Este projeto de Java cria um projeto novo de deteção de objetos da Visão Personalizada denominado __Sample Java OD Project__, que pode ser acedido a partir do [site da Visão Personalizada](https://customvision.ai/). Em seguida, a aplicação carrega imagens para preparar e testar um classificador. Neste projeto, o classificador tem como objetivo determinar se uma árvore é uma __cicuta__ ou uma __cerejeira japonesa__.

[!INCLUDE [get-keys](includes/get-keys.md)]

O programa está configurado para armazenar os seus dados mais importantes como variáveis de ambiente. Navegue para a pasta **Vision/CustomVision**, no PowerShell, para definir estas variáveis. Em seguida, introduza os comandos:

```PowerShell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>Compreender o código

Carregue o projeto `Vision/CustomVision` no IDE Java e abra o ficheiro _CustomVisionSamples.java_. Localize o método **runSample** e comente a chamada do método **ImageClassification_Sample** &mdash; esta ação executa o cenário de classificação de imagens, que não é abordado neste guia. O método **ObjectDetection_Sample** implementa a funcionalidade principal deste início rápido; navegue para a respetiva definição e inspecione o código. 

### <a name="create-a-new-custom-vision-service-project"></a>Criar um novo projeto do Serviço de Visão Personalizada

Vá para o bloco de código que cria um cliente de preparação e um projeto de deteção de objetos. O projeto criado aparece no [site da Visão Personalizada](https://customvision.ai/) ao qual acedeu anteriormente. 

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=181-206)]

### <a name="add-tags-to-your-project"></a>Adicionar etiquetas ao seu projeto

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=208-218)]

### <a name="upload-and-tag-images"></a>Carregar e etiquetar imagens

Ao etiquetar imagens em projetos de deteção de objeto, tem de especificar a região de cada objeto etiquetado com coordenadas normalizadas. Vá para a definição do Mapa `regionMap`. Este código associa cada uma das imagens de exemplo à sua região marcada.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=130-179)]

Em seguida, avance para o bloco de código que adiciona as imagens ao projeto. As imagens são lidas a partir da pasta **src/main/resources** do projeto e são carregadas para o serviço com as respetivas etiquetas adequadas e as coordenadas de região.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=220-231)]

O fragmento de código anterior utiliza duas funções de programa auxiliar que obtêm as imagens como fluxos de recursos e as carrega para o serviço.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=277-314)]

### <a name="train-the-project"></a>Preparar o projeto

Este código cria a primeira iteração no projeto e marca-a como a iteração predefinida. A iteração predefinida reflete a versão do modelo que irá responder aos pedidos de predição. Deve atualizá-la sempre que voltar a preparar o modelo.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=233-242)]

### <a name="use-the-prediction-endpoint"></a>Utilizar o ponto final de predição

O ponto final de predição, representado pelo objeto `predictor` aqui, é a referência que vai servir para submeter uma imagem para o modelo atual e obter uma predição de classificação. Neste exemplo, `predictor` é definido noutro sítio através da utilização da variável de ambiente da chave de predição.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=244-270)]

## <a name="run-the-application"></a>Executar a aplicação

Para compilar e executar a solução com o maven, execute o comando seguinte no diretório do projeto no PowerShell:

```PowerShell
mvn compile exec:java
```

Veja o resultado da consola para o registo e predição de resultados. Pode, em seguida, certificar-se de que a imagem de teste está identificada de forma adequada e que a região de deteção está correta.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Passos Seguintes

Agora viu como cada passo do processo de deteção de objeto pode ser feito no código. Este exemplo executa uma iteração de preparação individual, mas, muitas vezes, terá de preparar e testar o seu modelo várias vezes para torná-lo mais preciso. O guia seguinte aborda a classificação de imagens, mas os seus princípios são semelhantes à deteção de objetos.

> [!div class="nextstepaction"]
> [Test and retrain a model](test-your-model.md) (Testar e voltar a preparar um modelo)
