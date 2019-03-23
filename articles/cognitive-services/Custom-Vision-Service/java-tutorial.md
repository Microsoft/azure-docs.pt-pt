---
title: 'Início rápido: Criar um projeto de classificação de imagem com o SDK de visão personalizada para Java'
titlesuffix: Azure Cognitive Services
description: Crie um projeto, adicione etiquetas, carregue imagens, prepare o seu projeto e faça uma predição com o SDK para Java.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: areddish
ms.openlocfilehash: 89683b0fe5d401a92dc8575fd1ddc81d74d594b6
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351155"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-sdk-for-java"></a>Início rápido: Criar um projeto de classificação de imagem com o SDK de visão personalizada para Java

Este artigo apresenta informações e código de exemplo para o ajudar a começar a utilizar o SDK da Visão Personalizada para Java e compilar um modelo d classificação de imagens. Depois de criado, poderá adicionar etiquetas, carregar imagens, preparar o projeto, obter o URL de ponto final de predição predefinido do projeto e utilizar o ponto final para testar uma imagem de forma programática. Utilize este exemplo como um modelo para compilar a sua aplicação de Java. Se quiser percorrer o processo de compilar e utilizar um modelo de classificação _sem_ recorrer a código, veja antes as [orientações baseadas no browser](getting-started-build-a-classifier.md).

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

Este projeto de Java cria um projeto novo de classificação de imagens da Visão Personalizada denominado __Sample Java Project__, que pode ser acedido a partir do [site da Visão Personalizada](https://customvision.ai/). Em seguida, a aplicação carrega imagens para preparar e testar um classificador. Neste projeto, o classificador tem como objetivo determinar se uma árvore é uma __cicuta__ ou uma __cerejeira japonesa__.

[!INCLUDE [get-keys](includes/get-keys.md)]

O programa está configurado para armazenar os seus dados mais importantes como variáveis de ambiente. Navegue para a pasta **Vision/CustomVision**, no PowerShell, para definir estas variáveis. Em seguida, introduza os comandos:

```PowerShell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>Compreender o código

Carregue o projeto `Vision/CustomVision` no IDE Java e abra o ficheiro _CustomVisionSamples.java_. Localize o método **runSample** e comente a chamada do método **ObjectDetection_Sample** &mdash; esta ação executa o cenário de deteção de objetos, que não é abordado neste guia. O método **ImageClassification_Sample** implementa a funcionalidade principal deste exemplo; navegue para a respetiva definição e inspecione o código.

### <a name="create-a-custom-vision-service-project"></a>Criar um projeto do Serviço de Visão Personalizada

A primeira parte do código cria um projeto de classificação de imagens. O projeto criado aparece no [site da Visão Personalizada](https://customvision.ai/) ao qual acedeu anteriormente. 

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=57-63)]

### <a name="create-tags-in-the-project"></a>Criar etiquetas no projeto

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=65-74)]

### <a name="upload-and-tag-images"></a>Carregar e etiquetar imagens

As imagens de exemplo estão incluídas na pasta **src/main/resources** do projeto. São lidas a partir daí e carregadas para o serviço com as etiquetas adequadas.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=76-87)]

O fragmento de código anterior utiliza duas funções de programa auxiliar que obtêm as imagens como fluxos de recursos e as carrega para o serviço.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=277-314)]

### <a name="train-the-classifier"></a>Preparar o classificador

Este código cria a primeira iteração no projeto e marca-a como a iteração predefinida. A iteração predefinida reflete a versão do modelo que irá responder aos pedidos de predição. Deve atualizá-la sempre que voltar a preparar o modelo.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=89-99)]

### <a name="use-the-prediction-endpoint"></a>Utilizar o ponto final de predição

O ponto final de predição, representado pelo objeto `predictor` aqui, é a referência que vai servir para submeter uma imagem para o modelo atual e obter uma predição de classificação. Neste exemplo, `predictor` é definido noutro sítio através da utilização da variável de ambiente da chave de predição.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=108-120)]

## <a name="run-the-application"></a>Executar a aplicação

Para compilar e executar a solução com o maven, execute o comando seguinte no diretório do projeto no PowerShell:

```PowerShell
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

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Passos Seguintes

Viu como cada passo do processo de classificação de imagens pode ser feito no código. Este exemplo executa uma iteração de preparação individual, mas, muitas vezes, terá de preparar e testar o seu modelo várias vezes para torná-lo mais preciso.

> [!div class="nextstepaction"]
> [Test and retrain a model](test-your-model.md) (Testar e voltar a preparar um modelo)
