---
title: 'Quickstart: Biblioteca de clientes de reconhecimento de caracteres óticos para Python'
description: Começa com a biblioteca de clientes de reconhecimento de caracteres óticos para python com este arranque rápido.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: d0b2a854391097cc7d95c4286ba581f3660d397e
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284828"
---
<a name="HOLTop"></a>

Utilize a biblioteca de clientes de reconhecimento de caracteres óticos para ler texto impresso e manuscrito com a API de leitura.

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

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo no [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py)que contém os exemplos de código neste arranque rápido.

Crie um novo ficheiro Python &mdash; *quickstart-file.py,* por exemplo. Em seguida, abra-o no seu editor preferido ou IDE.

### <a name="find-the-subscription-key-and-endpoint"></a>Encontre a chave de subscrição e o ponto final

Aceda ao portal do Azure. Se o recurso de Visão De Computador que criou na secção **Pré-Requisitos** implementado com sucesso, clique no botão **'Ir a Recursos'** nos **Passos Seguintes**. Pode encontrar a chave de subscrição e o ponto final na **página chave e ponto final** do recurso, sob **gestão de recursos.** 

Crie variáveis para a sua chave de subscrição de Visão de Computador e ponto final. Cole a chave de subscrição e o ponto final no seguinte código, quando indicado. O seu ponto final de visão de computador tem o formulário `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Lembre-se de remover a chave de subscrição do seu código quando terminar e nunca postá-la publicamente. Para a produção, considere utilizar uma forma segura de armazenar e aceder às suas credenciais. Por exemplo, [cofre de chaves Azure](../../../../key-vault/general/overview.md).

> [!div class="nextstepaction"]
> [Criei o cliente](?success=set-up-client#object-model) [que encontrei um problema.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=set-up-client)

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces lidam com algumas das principais características do OCR Python SDK.

|Nome|Descrição|
|---|---|
|[ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin)| Esta classe lida diretamente com todas as operações de imagem, tais como análise de imagem, deteção de texto e geração de miniaturas.|
| [ComputadorVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) | Esta classe é necessária para todas as funcionalidades de Visão de Computador. Você instantanea-o com as suas informações de subscrição, e você usá-lo para produzir instâncias de outras classes. Implementa **ComputerVisionClientOperationsMixin.**|
|[VisualFeatureTypes](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| Este enum define os diferentes tipos de análise de imagem que podem ser feitos numa operação de Análise padrão. Especifica um conjunto de **valores VisualFeatureTypes** dependendo das suas necessidades. |

## <a name="code-examples"></a>Exemplos de código

Estes fragmentos de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes OCR para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Ler texto impresso e manuscrito](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Instantiar um cliente com o seu ponto final e chave. Crie um objeto [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) com a sua chave e use-o com o seu ponto final para criar um objeto [ComputerVisionClient.](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

> [!div class="nextstepaction"]
> [Autensei o cliente](?success=authenticate-client#read-printed-and-handwritten-text) [que encontrei num problema.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=authenticate-client)

## <a name="read-printed-and-handwritten-text"></a>Ler texto impresso e manuscrito

O serviço OCR pode ler texto visível numa imagem e convertê-lo num fluxo de caracteres. Faça isto em duas partes.

### <a name="call-the-read-api"></a>Ligue para a API de leitura

Em primeiro lugar, utilize o seguinte código para ligar para o método **de leitura** para a imagem dada. Isto devolve um ID de operação e inicia um processo assíncrona para ler o conteúdo da imagem.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_call)]

> [!TIP]
> Também pode ler texto a partir de uma imagem local. Consulte os [métodos ComputerVisionClientOperationsMixin,](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin) tais como **read_in_stream**. Ou, consulte o código de amostra no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py) para cenários que envolvam imagens locais.

### <a name="get-read-results"></a>Obter resultados de Leitura

Em seguida, obtenha o ID de operação devolvido da chamada **de leitura,** e use-o para consultar o serviço para obter resultados de operação. O código seguinte verifica a operação em intervalos de um segundo até que os resultados sejam devolvidos. Em seguida, imprime os dados de texto extraídos para a consola.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_response)]

> [!div class="nextstepaction"]
> [Li um sms](?success=read-printed-handwritten-text#run-the-application) [que encontrei um problema.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=read-printed-handwritten-text)

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

Neste arranque rápido, aprendeu a usar a biblioteca OCR para Python para fazer tarefas de base. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
>[Referência OCR API (Python)](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)

* [Visão geral do OCR](../../overview-ocr.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py).
