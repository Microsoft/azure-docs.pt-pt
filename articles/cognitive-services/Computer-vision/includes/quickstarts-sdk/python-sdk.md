---
title: 'Quickstart: Biblioteca de clientes computer vision para Python'
description: Comece com a biblioteca de clientes da Computer Vision para python com este arranque rápido.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 5f552dfc320459d23e538e65e5f34c085f5b5ad9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80136008"
---
<a name="HOLTop"></a>

[Documentação de referência](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision) | Pacote[de código fonte](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision) | da biblioteca[(PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-computervision/) | [Samples](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Configuração

### <a name="create-a-computer-vision-azure-resource"></a>Criar um recurso Computer Vision Azure

Os Serviços Cognitivos Azure são representados por recursos Azure que subscreve. Crie um recurso para a Visão Computacional utilizando o [portal Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [o Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na sua máquina local. Também pode:

* Obtenha uma chave de [teste](https://azure.microsoft.com/try/cognitive-services/#decision) válida por sete dias de graça. Depois de se inscrever, estará disponível no site do [Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  
* Veja o seu recurso no [portal Azure](https://portal.azure.com/)

Depois de obter uma chave da sua subscrição ou recurso experimental, [crie variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para o URL chave e final, nomeado `COMPUTER_VISION_SUBSCRIPTION_KEY` e, `COMPUTER_VISION_ENDPOINT`respectivamente.
 
### <a name="create-a-new-python-application"></a>Criar uma aplicação Python nova

Crie um&mdash;novo roteiro Python*quickstart-file.py,* por exemplo. Em seguida, abra-a no seu editor preferido ou IDE e importe as seguintes bibliotecas.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports)]

Em seguida, crie variáveis para o ponto final e chave Azure do seu recurso.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_vars)]

> [!NOTE]
> Se criou a variável ambiental depois de lançar a aplicação, terá de fechar e reabrir o editor, IDE, ou a shell executá-la para aceder à variável.

### <a name="install-the-client-library"></a>Instale a biblioteca do cliente

Pode instalar a biblioteca do cliente com:

```console
pip install --upgrade azure-cognitiveservices-vision-computervision
```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características do Computer Vision Python SDK.

|Nome|Descrição|
|---|---|
|[ComputerVisionClientOperationsMixin](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin?view=azure-python)| Esta classe lida diretamente com todas as operações de imagem, tais como análise de imagem, deteção de texto e geração de miniaturas.|
| [ComputerVisionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) | Esta aula é necessária para todas as funcionalidades de Visão Computacional. Você instantaneamente com a sua informação de subscrição, e você usa-o para produzir casos de outras classes. Implementa **o ComputerVisionClientOperationsMixin**.|
|[Tipos de Características Visuais](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python)| Este enum define os diferentes tipos de análise de imagem que podem ser feitos numa operação padrão da Análise. Especifica um conjunto de valores **VisualFeatureTypes** dependendo das suas necessidades. |

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes da Computer Vision para python:

* [Autenticar o cliente](#authenticate-the-client)
* [Analisar uma imagem](#analyze-an-image)
* [Ler texto impresso e manuscrito](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este quickstart assume que [criou uma variável ambiental](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) para `COMPUTER_VISION_SUBSCRIPTION_KEY`a sua chave Computer Vision, chamada .

Instantifique um cliente com o seu ponto final e chave. Crie um objeto [CognitiveServicesCredenciais](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) com a sua chave e use-o com o seu ponto final para criar um objeto [ComputerVisionClient.](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

## <a name="analyze-an-image"></a>Analisar uma imagem

Guarde uma referência ao URL de uma imagem que pretende analisar.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_remoteimage)]

### <a name="get-image-description"></a>Obtenha descrição da imagem

O código seguinte obtém a lista de legendas geradas para a imagem. Consulte [imagens de descrever](../../concept-describing-images.md) para mais detalhes.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_describe)]

### <a name="get-image-category"></a>Obtenha categoria de imagem

O código seguinte obtém a categoria detetada da imagem. Consulte [as imagens do Categorize](../../concept-categorizing-images.md) para mais detalhes.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_categorize)]

### <a name="get-image-tags"></a>Obtenha etiquetas de imagem

O código seguinte obtém o conjunto de etiquetas detetadas na imagem. Consulte [as etiquetas de Conteúdo](../../concept-tagging-images.md) para mais detalhes.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_tags)]

### <a name="detect-objects"></a>Detetar objetos

O código seguinte deteta objetos comuns na imagem e imprime-os na consola. Consulte a [deteção de objetos](../../concept-object-detection.md) para obter mais detalhes.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_objects)]

### <a name="detect-brands"></a>Detetar marcas

O código seguinte deteta marcas e logótipos corporativos na imagem e imprime-os para a consola. Consulte a [deteção da marca](../../concept-brand-detection.md) para obter mais detalhes.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_brands)]

### <a name="detect-faces"></a>Detetar rostos

O código seguinte devolve os rostos detetados na imagem com as suas coordenadas de retângulo e seleciona atributos faciais. Consulte a [deteção do rosto](../../concept-detecting-faces.md) para obter mais detalhes.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detetar conteúdo adulto, picante ou sangrento

O código seguinte imprime a presença detetada de conteúdo adulto na imagem. Consulte [conteúdo adulto, picante e sangrento](../../concept-detecting-adult-content.md) para mais detalhes.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Obtenha esquema de cor de imagem

O código que se segue imprime os atributos de cor detetados na imagem, como as cores dominantes e a cor do sotaque. Consulte [esquemas de cores](../../concept-detecting-color-schemes.md) para mais detalhes.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Obtenha conteúdo específico do domínio

A Computer Vision pode usar um modelo especializado para fazer uma análise mais aprofundada das imagens. Consulte [conteúdo específico do Domínio](../../concept-detecting-domain-content.md) para obter mais detalhes. 

O código que se segue analisa dados sobre celebridades detetadas na imagem.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_celebs)]

O código que se segue analisa os dados sobre marcos detetados na imagem.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Obter o tipo de imagem

O código que se segue imprime informações sobre o tipo de imagem,&mdash;quer se trate de clip art ou de desenho de linha.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Ler texto impresso e manuscrito

A Visão computorizada pode ler texto visível numa imagem e convertê-lo num fluxo de caracteres. Faz-se isto em duas partes.

### <a name="call-the-read-api"></a>Ligue para a API read

Em primeiro lugar, utilize o seguinte código para chamar o método **batch_read_file** para a imagem dada. Isto devolve um ID de operação e inicia um processo assíncrono para ler o conteúdo da imagem.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_call)]

### <a name="get-read-results"></a>Obter resultados de leitura

Em seguida, retire o ID de operação da **batch_read_file** chamada e use-o para consultar o serviço para obter resultados de operação. O código seguinte verifica a operação em intervalos de um segundo até que os resultados sejam devolvidos. Em seguida, imprime os dados de texto extraídos para a consola.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_response)]

## <a name="run-the-application"></a>Executar a aplicação

Execute o `python` pedido com o comando no seu ficheiro de arranque rápido.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos associados ao mesmo.

* [Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)


## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar a biblioteca Computer Vision para python para fazer tarefas base. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.


> [!div class="nextstepaction"]
>[Referência a API da Visão Computacional (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)

* [O que é a Imagem Digitalizada?](../../Home.md)
* O código fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py).
