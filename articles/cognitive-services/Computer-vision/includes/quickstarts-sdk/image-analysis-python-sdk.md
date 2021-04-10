---
title: 'Quickstart: Biblioteca de clientes de análise de imagem para Python'
description: Começa com a biblioteca de clientes image Analysis para Python com este arranque rápido.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: aae780ad42c39789c0c4448a90a12737074e4176
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073718"
---
<a name="HOLTop"></a>

Utilize a biblioteca do cliente de Análise de Imagem para analisar uma imagem para tags, descrição de texto, rostos, conteúdo adulto e muito mais.

[Documentação de referência](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision)  |  [Pacote (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-computervision/)  |  [Amostras](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * A sua instalação Python deve incluir [pip](https://pip.pypa.io/en/stable/). Pode verificar se tem pip instalado em funcionamento `pip --version` na linha de comando. Obtenha pip instalando a versão mais recente de Python.
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" crie um recurso de Visão de Computador crie um recurso de "  target="_blank"> Visão De Computador no portal </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.

    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação ao serviço de Visão De Computador. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Pode instalar a biblioteca do cliente com:

```console
pip install --upgrade azure-cognitiveservices-vision-computervision
```

Instale também a biblioteca travesseiro.

```console
pip install pillow
```

### <a name="create-a-new-python-application"></a>Criar uma aplicação Python nova

Crie um novo ficheiro Python &mdash; *quickstart-file.py,* por exemplo. Em seguida, abra-o no seu editor preferido ou IDE e importe as seguintes bibliotecas.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports)]

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py)que contém os exemplos de código neste arranque rápido.

Em seguida, crie variáveis para o ponto final e chave Azure do seu recurso.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_vars)]

> [!IMPORTANT]
> Aceda ao portal do Azure. Se o recurso de Visão De Computador que criou na secção **Pré-Requisitos** implementado com sucesso, clique no botão **'Ir a Recursos'** nos **Passos Seguintes**. Pode encontrar a sua chave e ponto final na **página chave e ponto final** do recurso, sob **gestão de recursos.** 
>
> Lembre-se de remover a chave do seu código quando terminar, e nunca postá-la publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Por exemplo, [cofre de chaves Azure](../../../../key-vault/general/overview.md).

> [!div class="nextstepaction"]
> [Criei o cliente](?success=set-up-client#object-model) [que encontrei um problema.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=set-up-client)

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características do Python SDK de análise de imagem.

|Nome|Descrição|
|---|---|
|[ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin)| Esta classe lida diretamente com todas as operações de imagem, tais como análise de imagem, deteção de texto e geração de miniaturas.|
| [ComputadorVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) | Esta classe é necessária para todas as funcionalidades de Visão de Computador. Você instantanea-o com as suas informações de subscrição, e você usá-lo para produzir instâncias de outras classes. Implementa **ComputerVisionClientOperationsMixin.**|
|[VisualFeatureTypes](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| Este enum define os diferentes tipos de análise de imagem que podem ser feitos numa operação de Análise padrão. Especifica um conjunto de **valores VisualFeatureTypes** dependendo das suas necessidades. |

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes de Análise de Imagem para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Analisar uma imagem](#analyze-an-image)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Instantiar um cliente com o seu ponto final e chave. Crie um objeto [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) com a sua chave e use-o com o seu ponto final para criar um objeto [ComputerVisionClient.](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

> [!div class="nextstepaction"]
> [Autensei o cliente](?success=authenticate-client#analyze-an-image) [que encontrei num problema.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=authenticate-client)

## <a name="analyze-an-image"></a>Analisar uma imagem

Use o seu objeto cliente para analisar as características visuais de uma imagem remota. Primeiro guarde uma referência ao URL de uma imagem que pretende analisar.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_remoteimage)]

> [!TIP]
> Também pode analisar uma imagem local. Consulte os [métodos ComputerVisionClientOperationsMixin,](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin) tais como **analyze_image_in_stream**. Ou, consulte o código de amostra no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py) para cenários que envolvam imagens locais.

### <a name="get-image-description"></a>Obtenha a descrição da imagem

O seguinte código obtém a lista de legendas geradas para a imagem. Consulte [as imagens](../../concept-describing-images.md) para mais detalhes.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_describe)]

### <a name="get-image-category"></a>Obtenha categoria de imagem

O seguinte código obtém a categoria detetada da imagem. Consulte [as imagens categorize](../../concept-categorizing-images.md) para mais detalhes.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_categorize)]

### <a name="get-image-tags"></a>Obtenha etiquetas de imagem

O seguinte código obtém o conjunto de tags detetadas na imagem. Consulte [as etiquetas de conteúdo](../../concept-tagging-images.md) para mais detalhes.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_tags)]

### <a name="detect-objects"></a>Detetar objetos

O código a seguir deteta objetos comuns na imagem e imprime-os na consola. Consulte [a deteção de objetos](../../concept-object-detection.md) para obter mais detalhes.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_objects)]        

### <a name="detect-brands"></a>Detetar marcas

O código que se segue deteta marcas e logótipos corporativos na imagem e imprime-os na consola. Consulte [a deteção da marca](../../concept-brand-detection.md) para mais detalhes.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_brands)]

### <a name="detect-faces"></a>Detetar rostos

O código seguinte devolve as faces detetadas na imagem com as suas coordenadas de retângulo e seleciona atributos faciais. Consulte [a deteção do rosto](../../concept-detecting-faces.md) para obter mais detalhes.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detetar conteúdo adulto, picante ou sangrento

O código que se segue imprime a presença detetada de conteúdo adulto na imagem. Consulte [o conteúdo adulto, picante e sangrento](../../concept-detecting-adult-content.md) para mais detalhes.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Obtenha esquema de cores de imagem

O código seguinte imprime os atributos de cor detetados na imagem, como as cores dominantes e a cor do acento. Consulte [os esquemas de cores](../../concept-detecting-color-schemes.md) para obter mais detalhes.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Obtenha conteúdo específico de domínio

A Image Analysis pode usar um modelo especializado para fazer mais análises nas imagens. Consulte [o conteúdo específico do domínio](../../concept-detecting-domain-content.md) para obter mais detalhes. 

O código seguinte analisa dados sobre celebridades detetadas na imagem.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_celebs)]

O código seguinte analisa dados sobre marcos detetados na imagem.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Obtenha o tipo de imagem

O código que se segue imprime informações sobre o tipo de &mdash; imagem, quer se trate de clip art ou desenho de linha.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_type)]

> [!div class="nextstepaction"]
> [Analisei uma imagem](?success=analyze-image#run-the-application) [que encontrei num problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=analyze-image)



## <a name="run-the-application"></a>Executar a aplicação

Execute a aplicação com o `python` comando no seu ficheiro quickstart.

```console
python quickstart-file.py
```

> [!div class="nextstepaction"]
> [Corri o requerimento](?success=run-the-application#clean-up-resources) [que encontrei.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=run-the-application)

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Limpei os recursos](?success=clean-up-resources#next-steps) [que encontrei num problema.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a instalar a biblioteca de clientes de Análise de Imagem e a fazer chamadas básicas de análise de imagem. Em seguida, saiba mais sobre as funcionalidades da API de análise.

> [!div class="nextstepaction"]
>[Ligue para a API de Análise](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Visão geral da análise de imagem](../../overview-image-analysis.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py).
