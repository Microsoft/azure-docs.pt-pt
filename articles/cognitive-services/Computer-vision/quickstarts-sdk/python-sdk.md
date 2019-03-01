---
title: 'Início rápido: SDK Python'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, irá aprender a utilizar o SDK de Python para tarefas comuns.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: pafarley
ms.openlocfilehash: ffecc07c49db8fd1b27cc2dd82192aa31a7fbd19
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194984"
---
# <a name="azure-cognitive-services-computer-vision-sdk-for-python"></a>Visão do computador de serviços cognitivos do Azure SDK para Python

O serviço Imagem Digitalizada fornece aos programadores acesso a algoritmos avançados para processamento de imagens e devolução de informações. Algoritmos de visão do computador analisam o conteúdo de uma imagem de diversas formas, dependendo dos recursos visual que está interessado. 

* [Analisar uma imagem](#analyze-an-image)
* [Obter lista de domínio do requerente](#get-subject-domain-list)
* [Analisar uma imagem por domínio](#analyze-an-image-by-domain)
* [Obter a descrição de texto de uma imagem](#get-text-description-of-an-image)
* [Obter texto manuscrito de imagem](#get-text-from-image)
* [Gerar miniatura](#generate-thumbnail)

Para obter mais informações sobre este serviço, consulte [o que é o de imagem digitalizada?] [computervision_docs].

À procura de mais documentação?

* [Documentação de referência do SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)
* [Documentação de visão do computador de serviços cognitiva](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.6 +][python]
* Livre [chave de imagem digitalizada] [ computervision_resource] e região associada. Vai precisar destes valores quando criar a instância dos [ComputerVisionAPI] [ ref_computervisionclient] objeto cliente. Utilize um dos seguintes métodos para obter esses valores. 

### <a name="if-you-dont-have-an-azure-subscription"></a>Se não tiver uma subscrição do Azure

Criar uma chave gratuita válido durante sete dias com o **[experimente] [ computervision_resource]** experiência para o serviço de visão do computador. Quando a chave é criada, copie o nome de chave e região. Vai precisar disto para [criar o cliente](#create-client).

Tenha o seguinte depois da chave é criada:

* Valor da chave: uma cadeia de 32 carateres com o formato de `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` 
* Região principais: o subdomínio do URL do ponto final, https://**westcentralus**. api.cognitive.microsoft.com

### <a name="if-you-have-an-azure-subscription"></a>Se tiver uma subscrição do Azure

O método mais fácil para criar um recurso na sua subscrição está a utilizar o seguinte procedimento [CLI do Azure] [ azure_cli] comando. Esta ação cria uma chave de serviço cognitivo que pode ser usada em muitos serviços cognitivos. Tem de escolher a _existente_ nome do grupo de recursos, por exemplo, "my-cogserv-group" e o recurso de visão do computador novo nome, por exemplo, "meu computador-visão-recursos". 

```Bash
RES_REGION=westeurope 
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

az cognitiveservices account create \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --location $RES_REGION \
    --kind CognitiveServices \
    --sku S0 \
    --yes
```

<!--
## Installation

Install the Azure Cognitive Services Computer Vision SDK with [pip][pip], optionally within a [virtual environment][venv].

### Configure a virtual environment (optional)

Although not required, you can keep your base system and Azure SDK environments isolated from one another if you use a [virtual environment][virtualenv]. Execute the following commands to configure and then enter a virtual environment with [venv][venv], such as `cogsrv-vision-env`:

```Bash
python3 -m venv cogsrv-vision-env
source cogsrv-vision-env/bin/activate
```
-->

### <a name="install-the-sdk"></a>Instalar o SDK

Instalar o SDK da visão computador dos serviços cognitivos do Azure para Python [pacote] [ pypi_computervision] com [pip][pip]:

```Bash
pip install azure-cognitiveservices-vision-computervision
```

## <a name="authentication"></a>Authentication

Depois de criar o recurso de imagem digitalizada, terá de seus **região**e um dos seus **chaves de contas** para instanciar o objeto de cliente.

Utilize estes valores quando criar a instância dos [ComputerVisionAPI] [ ref_computervisionclient] objeto cliente. 

Por exemplo, utilize o Bash terminal para definir as variáveis de ambiente:

```Bash
ACCOUNT_REGION=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>
```

### <a name="for-azure-subscription-users-get-credentials-for-key-and-region"></a>Para os utilizadores de subscrição do Azure, obter as credenciais para a chave e região

Se não se lembra da sua região e a chave, pode utilizar o seguinte método para encontrá-los. Se precisar de criar uma chave e a região, pode utilizar o método para [proprietários da subscrição do Azure](#if-you-have-an-azure-subscription) ou para [utilizadores sem uma subscrição do Azure](#if-you-dont-have-an-azure-subscription).

Utilize o [CLI do Azure] [ cloud_shell] fragmento abaixo para preencher as duas variáveis de ambiente com a conta de imagem digitalizada **região** e uma das suas **chaves**(também pode encontrar estes valores no [portal do Azure][azure_portal]). O trecho de código é formatado para o shell de Bash.

```Bash
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

export ACCOUNT_REGION=$(az cognitiveservices account show \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query location \
    --output tsv)

export ACCOUNT_KEY=$(az cognitiveservices account keys list \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query key1 \
    --output tsv)
```


### <a name="create-client"></a>Criar cliente

Obter a região e a chave de variáveis de ambiente, em seguida, criar os [ComputerVisionAPI] [ ref_computervisionclient] objeto cliente.  

```Python
from azure.cognitiveservices.vision.computervision import ComputerVisionAPI
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials

# Get region and key from environment variables
import os
region = os.environ['ACCOUNT_REGION']
key = os.environ['ACCOUNT_KEY']

# Set credentials
credentials = CognitiveServicesCredentials(key)

# Create client
client = ComputerVisionAPI(region, credentials)
```

## <a name="examples"></a>Exemplos

É necessário um [ComputerVisionAPI] [ ref_computervisionclient] objeto de cliente antes de utilizar qualquer uma das seguintes tarefas.

### <a name="analyze-an-image"></a>Analisar uma imagem

Pode analisar uma imagem para determinadas funcionalidades com [ `analyze_image` ] [ ref_computervisionclient_analyze_image]. Utilize o [ `visual_features` ] [ ref_computervision_model_visualfeatures] propriedade a definir os tipos de análises a serem feitas na imagem. Valores comuns são `VisualFeatureTypes.tags` e `VisualFeatureTypes.description`.

```Python
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

image_analysis = client.analyze_image(url,visual_features=[VisualFeatureTypes.tags])

for tag in image_analysis.tags:
    print(tag)
```

### <a name="get-subject-domain-list"></a>Obter lista de domínio do requerente

Rever os domínios de sujeito usados para analisar a sua imagem com [ `list_models` ] [ ref_computervisionclient_list_models]. Estes nomes de domínio são utilizados quando [analisar uma imagem por domínio](#analyze-an-image-by-domain). Um exemplo de um domínio é `landmarks`.

```Python
models = client.list_models()

for x in models.models_property:
    print(x)
```

### <a name="analyze-an-image-by-domain"></a>Analisar uma imagem por domínio

Pode analisar uma imagem por domínio de assunto com [ `analyze_image_by_domain` ] [ ref_computervisionclient_analyze_image_by_domain]. Obter o [suportado de lista de domínios de sujeito](#get-subject-domain-list) para poder utilizar o nome de domínio correto.  

```Python
# type of prediction
domain = "landmarks"

# Public domain image of Eiffel tower
url = "https://images.pexels.com/photos/338515/pexels-photo-338515.jpeg"

# English language response
language = "en"

analysis = client.analyze_image_by_domain(domain, url, language)

for landmark in analysis.result["landmarks"]:
    print(landmark["name"])
    print(landmark["confidence"])
```

### <a name="get-text-description-of-an-image"></a>Obter a descrição de texto de uma imagem

Pode obter uma descrição de uma imagem com texto baseadas em linguagem [ `describe_image` ] [ ref_computervisionclient_describe_image]. Solicitar várias descrições com o `max_description` propriedade se estiver fazendo a análise de texto para palavras-chave associados à imagem. Exemplos de uma descrição de texto para a imagem seguinte `a train crossing a bridge over a body of water`, `a large bridge over a body of water`, e `a train crossing a bridge over a large body of water`.

```Python
domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

analysis = client.describe_image(url, max_descriptions, language)

for caption in analysis.captions:
    print(caption.text)
    print(caption.confidence)
```

### <a name="get-text-from-image"></a>Obter texto de imagem

Pode obter qualquer texto manuscrito ou impresso a partir de uma imagem. Isso requer duas chamadas para o SDK: [ `recognize_text` ] [ ref_computervisionclient_recognize_text] e [ `get_text_operation_result` ] [ ref_computervisionclient_get_text_operation_result]. A chamada para recognize_text é assíncrona. Nos resultados da chamada get_text_operation_result, precisa verificar se a primeira chamada foi concluída com [ `TextOperationStatusCodes` ] [ ref_computervision_model_textoperationstatuscodes] antes de extrair os dados de texto. Os resultados incluem o texto, bem como as coordenadas da caixa delimitadora para o texto. 

```Python
# import models
from azure.cognitiveservices.vision.computervision.models import TextRecognitionMode
from azure.cognitiveservices.vision.computervision.models import TextOperationStatusCodes

url = "https://azurecomcdn.azureedge.net/cvt-1979217d3d0d31c5c87cbd991bccfee2d184b55eeb4081200012bdaf6a65601a/images/shared/cognitive-services-demos/read-text/read-1-thumbnail.png"
mode = TextRecognitionMode.handwritten
raw = True
custom_headers = None
numberOfCharsInOperationId = 36

# Async SDK call
rawHttpResponse = client.recognize_text(url, mode, custom_headers,  raw)

# Get ID from returned headers
operationLocation = rawHttpResponse.headers["Operation-Location"]
idLocation = len(operationLocation) - numberOfCharsInOperationId
operationId = operationLocation[idLocation:]

# SDK call
while result.status in ['NotStarted', 'Running']:
    time.sleep(1)
    result = client.get_text_operation_result(operationId)

# Get data
if result.status == TextOperationStatusCodes.succeeded:

    for line in result.recognition_result.lines:
        print(line.text)
        print(line.bounding_box)
```

### <a name="generate-thumbnail"></a>Gerar miniatura

Pode gerar uma miniatura (JPG) de uma imagem com [ `generate_thumbnail` ] [ ref_computervisionclient_generate_thumbnail]. A miniatura não precisa de ter as mesmas proporções da imagem original. 

Instale **Pillow** para utilizar este exemplo:

```bash
pip install Pillow
``` 

Assim que Pillow estiver instalado, utilize o pacote no seguinte exemplo de código para gerar a imagem em miniatura.

```Python
# Pillow package
from PIL import Image

# IO package to create local image
import io

width = 50
height = 50
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"

thumbnail = client.generate_thumbnail(width, height, url)

for x in thumbnail:
    image = Image.open(io.BytesIO(x))

image.save('thumbnail.jpg')
```

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="general"></a>Geral

Quando interage com o [ComputerVisionAPI] [ ref_computervisionclient] objeto de cliente utilizando o SDK de Python, o [ `ComputerVisionErrorException` ] [ ref_computervision_computervisionerrorexception] classe é utilizada para devolva erros. Erros devolvidos pelo serviço correspondem para os mesmo códigos de estado HTTP devolvidos para pedidos de REST API.

Por exemplo, se tentar analisar uma imagem com uma chave inválida, um `401` erro é retornado. No seguinte fragmento, o [erro] [ ref_httpfailure] manipuladas com elegância, capturar a exceção e exibindo informações adicionais sobre o erro.

```Python

domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

try:
    analysis = client.describe_image(url, max_descriptions, language)

    for caption in analysis.captions:
        print(caption.text)
        print(caption.confidence)
except HTTPFailure as e:
    if e.status_code == 401:
        print("Error unauthorized. Make sure your key and region are correct.")
    else:
        raise
```

### <a name="handle-transient-errors-with-retries"></a>Lidar com erros transitórios com repetições

Ao trabalhar com o [ComputerVisionAPI] [ ref_computervisionclient] cliente, que poderá encontrar falhas transitórias causadas por [limites de velocidade] [ computervision_request_units] imposta o serviço, ou outros problemas transitórios como falhas de rede. Para obter informações sobre como lidar com esses tipos de falhas, consulte [padrão de repetição] [ azure_pattern_retry] no guia de padrões de conceção de nuvem e o relacionados [padrão disjuntor automático] [azure_pattern_circuit_breaker].

### <a name="more-sample-code"></a>Mais código de exemplo

Vários exemplos de SDK de Python de visão do computador estão disponíveis no repositório do GitHub do SDK. Estes exemplos fornecem código de exemplo para cenários adicionais comumente encontrados durante o trabalho com a visão do computador:

* [recognize_text][recognize-text]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Aplicação de conteúdo de marcas a imagens](../concept-tagging-images.md)

<!-- LINKS -->
[pip]: https://pypi.org/project/pip/
[python]: https://www.python.org/downloads/

[azure_cli]: https://docs.microsoft.com/en-us/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create
[azure_pattern_circuit_breaker]: https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker
[azure_pattern_retry]: https://docs.microsoft.com/azure/architecture/patterns/retry
[azure_portal]: https://portal.azure.com
[azure_sub]: https://azure.microsoft.com/free/

[cloud_shell]: https://docs.microsoft.com/azure/cloud-shell/overview

[venv]: https://docs.python.org/3/library/venv.html
[virtualenv]: https://virtualenv.pypa.io

[source_code]: https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-vision-computervision

[pypi_computervision]:https://pypi.org/project/azure-cognitiveservices-vision-computervision/
[pypi_pillow]:https://pypi.org/project/Pillow/

[ref_computervision_sdk]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision?view=azure-python
[ref_computervision_computervisionerrorexception]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.computervisionerrorexception?view=azure-python
[ref_httpfailure]: https://docs.microsoft.com/python/api/msrest/msrest.exceptions.httpoperationerror?view=azure-python


[computervision_resource]: https://azure.microsoft.com/en-us/try/cognitive-services/?

[computervision_docs]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/home

[ref_computervisionclient]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python


[ref_computervisionclient_analyze_image]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#analyze-image-url--visual-features-none--details-none--language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_list_models]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#list-models-custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_analyze_image_by_domain]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#analyze-image-by-domain-model--url--language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_describe_image]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#describe-image-url--max-candidates--1---language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_recognize_text]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#recognize-text-url--mode--custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_get_text_operation_result]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#get-text-operation-result-operation-id--custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_generate_thumbnail]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#generate-thumbnail-width--height--url--smart-cropping-false--custom-headers-none--raw-false--callback-none----operation-config-


[ref_computervision_model_visualfeatures]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python

[ref_computervision_model_textoperationstatuscodes]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.textoperationstatuscodes?view=azure-python

[computervision_request_units]:https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/

[recognize-text]:https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/vision/computer_vision_samples.py

