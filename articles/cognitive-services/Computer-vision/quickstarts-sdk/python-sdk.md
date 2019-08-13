---
title: 'Início rápido: SDK Python'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você aprende a usar o SDK do Python para tarefas comuns, como analisar imagem, obter descrição, reconhecer texto e gerar miniatura.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: c03568ece97bdaad86f4564debf9f3b2fa14c6ed
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "67786637"
---
# <a name="azure-cognitive-services-computer-vision-sdk-for-python"></a>SDK Pesquisa Visual Computacional de serviços cognitivas do Azure para Python

O serviço Imagem Digitalizada fornece aos programadores acesso a algoritmos avançados para processamento de imagens e devolução de informações. Os algoritmos de Pesquisa Visual Computacional analisam o conteúdo de uma imagem de maneiras diferentes, dependendo dos recursos visuais nos quais você está interessado.

* [Analisar uma imagem](#analyze-an-image)
* [Obter lista de domínios do assunto](#get-subject-domain-list)
* [Analisar uma imagem por domínio](#analyze-an-image-by-domain)
* [Obter descrição de texto de uma imagem](#get-text-description-of-an-image)
* [Obter texto manuscrito da imagem](#get-text-from-image)
* [Gerar miniatura](#generate-thumbnail)

Para obter mais informações sobre esse serviço, consulte [o que é pesquisa Visual computacional?][computervision_docs].

Procurando mais documentação?

* [Documentação de referência do SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)
* [Documentação de Pesquisa Visual Computacional de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.6 +][python]
* Pesquisa Visual Computacional gratuito de [chave][computervision_resource] e o ponto de extremidade associado. Você precisará desses valores ao criar a instância do objeto cliente [ComputerVisionClient][ref_computervisionclient] . Use um dos métodos a seguir para obter esses valores.

### <a name="if-you-dont-have-an-azure-subscription"></a>Se você não tiver uma assinatura do Azure

Crie uma chave gratuita válida por 7 dias com a experiência **[experimentar][computervision_resource]** para o serviço de pesquisa Visual computacional. Quando a chave for criada, copie a chave e o nome do ponto de extremidade. Você precisará disso para [criar o cliente](#create-client).

Mantenha o seguinte depois que a chave for criada:

* Valor de chave: uma cadeia de caracteres de 32 caracteres com o formato de`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`
* Ponto de extremidade de chave: a URL de\:ponto de extremidade base, https//westcentralus.API.cognitive.Microsoft.com

### <a name="if-you-have-an-azure-subscription"></a>Se você tiver uma assinatura do Azure

O método mais fácil para criar um recurso em sua assinatura é usar o comando [CLI do Azure][azure_cli] a seguir. Isso cria uma chave de serviço cognitiva que pode ser usada em vários serviços cognitivas. Você precisa escolher o nome do grupo de recursos _existente_ , por exemplo, "My-cogserv-Group" e o novo nome do recurso da pesquisa Visual computacional, como "My-Computer-Vision-Resource".

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

Instale o SDK do Pesquisa Visual Computacional de serviços cognitivas do Azure para [pacote][pypi_computervision] do Python com [Pip][pip]:

```Bash
pip install azure-cognitiveservices-vision-computervision
```

## <a name="authentication"></a>Authentication

Depois de criar o recurso de Pesquisa Visual Computacional, você precisa de seu **ponto de extremidade**e uma de suas **chaves de conta** para instanciar o objeto de cliente.

Use esses valores ao criar a instância do objeto cliente [ComputerVisionClient][ref_computervisionclient] .

Por exemplo, use o terminal Bash para definir as variáveis de ambiente:

```Bash
ACCOUNT_ENDPOINT=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>
```

### <a name="for-azure-subscription-users-get-credentials-for-key-and-endpoint"></a>Para usuários de assinatura do Azure, obtenha as credenciais para a chave e o ponto de extremidade

Se você não se lembrar de seu ponto de extremidade e chave, poderá usar o método a seguir para encontrá-lo. Se você precisar criar uma chave e um ponto de extremidade, poderá usar o método para os detentores de [assinatura do Azure](#if-you-have-an-azure-subscription) ou para [usuários sem uma assinatura do Azure](#if-you-dont-have-an-azure-subscription).

Use o trecho de [CLI do Azure][cloud_shell] abaixo para preencher duas variáveis de ambiente com o **ponto de extremidade** da conta de pesquisa Visual computacional e uma de suas **chaves** (você também pode encontrar esses valores no [portal do Azure][azure_portal]). O trecho de código é formatado para o shell bash.

```Bash
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

export ACCOUNT_ENDPOINT=$(az cognitiveservices account show \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query endpoint \
    --output tsv)

export ACCOUNT_KEY=$(az cognitiveservices account keys list \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query key1 \
    --output tsv)
```


### <a name="create-client"></a>Criar cliente

Obtenha o ponto de extremidade e a chave de variáveis de ambiente e crie o objeto de cliente [ComputerVisionClient][ref_computervisionclient] .

```Python
from azure.cognitiveservices.vision.computervision import ComputerVisionClient
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials

# Get endpoint and key from environment variables
import os
endpoint = os.environ['ACCOUNT_ENDPOINT']
key = os.environ['ACCOUNT_KEY']

# Set credentials
credentials = CognitiveServicesCredentials(key)

# Create client
client = ComputerVisionClient(endpoint, credentials)
```

## <a name="examples"></a>Exemplos

Você precisa de um objeto de cliente [ComputerVisionClient][ref_computervisionclient] antes de usar qualquer uma das tarefas a seguir.

### <a name="analyze-an-image"></a>Analisar uma imagem

Você pode analisar uma imagem para determinados recursos com [`analyze_image`][ref_computervisionclient_analyze_image]o. Use a [`visual_features`][ref_computervision_model_visualfeatures] propriedade para definir os tipos de análise a serem executados na imagem. Os valores comuns `VisualFeatureTypes.tags` são `VisualFeatureTypes.description`e.

```Python
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

image_analysis = client.analyze_image(url,visual_features=[VisualFeatureTypes.tags])

for tag in image_analysis.tags:
    print(tag)
```

### <a name="get-subject-domain-list"></a>Obter lista de domínios do assunto

Examine os domínios de assunto usados para analisar sua imagem [`list_models`][ref_computervisionclient_list_models]com o. Esses nomes de domínio são usados ao [analisar uma imagem por domínio](#analyze-an-image-by-domain). Um exemplo de domínio é `landmarks`.

```Python
models = client.list_models()

for x in models.models_property:
    print(x)
```

### <a name="analyze-an-image-by-domain"></a>Analisar uma imagem por domínio

Você pode analisar uma imagem por domínio de assunto [`analyze_image_by_domain`][ref_computervisionclient_analyze_image_by_domain]com. Obtenha a [lista de domínios de entidade com suporte](#get-subject-domain-list) para usar o nome de domínio correto.

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

### <a name="get-text-description-of-an-image"></a>Obter descrição de texto de uma imagem

Você pode obter uma descrição de texto com base em idioma de uma [`describe_image`][ref_computervisionclient_describe_image]imagem com. Solicite várias descrições com `max_description` a propriedade se estiver fazendo uma análise de texto para palavras-chave associadas à imagem. Exemplos de uma descrição de texto para a imagem a `a train crossing a bridge over a body of water`seguir `a large bridge over a body of water`incluem, `a train crossing a bridge over a large body of water`e.

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

### <a name="get-text-from-image"></a>Obter texto da imagem

Você pode obter qualquer texto manuscrito ou impresso de uma imagem. Isso requer duas chamadas para o SDK: [`batch_read_file`](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) e [`get_read_operation_result`](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python). A chamada para `batch_read_file` é assíncrona. Nos resultados da `get_read_operation_result` chamada, você precisa verificar se a primeira chamada foi concluída com [`TextOperationStatusCodes`][ref_computervision_model_textoperationstatuscodes] antes de extrair os dados de texto. Os resultados incluem o texto, bem como as coordenadas da caixa delimitadora para o texto.

```Python
# import models
from azure.cognitiveservices.vision.computervision.models import TextOperationStatusCodes
import time

url = "https://azurecomcdn.azureedge.net/cvt-1979217d3d0d31c5c87cbd991bccfee2d184b55eeb4081200012bdaf6a65601a/images/shared/cognitive-services-demos/read-text/read-1-thumbnail.png"
raw = True
custom_headers = None
numberOfCharsInOperationId = 36

# Async SDK call
rawHttpResponse = client.batch_read_file(url, custom_headers,  raw)

# Get ID from returned headers
operationLocation = rawHttpResponse.headers["Operation-Location"]
idLocation = len(operationLocation) - numberOfCharsInOperationId
operationId = operationLocation[idLocation:]

# SDK call
while True:
    result = client.get_read_operation_result(operationId)
    if result.status not in ['NotStarted', 'Running']:
        break
    time.sleep(1)

# Get data
if result.status == TextOperationStatusCodes.succeeded:
    for textResult in result.recognition_results:
        for line in textResult.lines:
            print(line.text)
            print(line.bounding_box)
```

### <a name="generate-thumbnail"></a>Gerar miniatura

Você pode gerar uma miniatura (JPG) de uma imagem com [`generate_thumbnail`][ref_computervisionclient_generate_thumbnail]. A miniatura não precisa estar nas mesmas proporções que a imagem original.

Instale o **Pillow** para usar este exemplo:

```bash
pip install Pillow
```

Quando o Pillow estiver instalado, use o pacote no exemplo de código a seguir para gerar a imagem em miniatura.

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

Quando você interage com o objeto de cliente [ComputerVisionClient][ref_computervisionclient] usando o SDK do Python [`ComputerVisionErrorException`][ref_computervision_computervisionerrorexception] , a classe é usada para retornar erros. Os erros retornados pelo serviço correspondem aos mesmos códigos de status HTTP retornados para solicitações da API REST.

Por exemplo, se você tentar analisar uma imagem com uma chave inválida, um `401` erro será retornado. No trecho a seguir, o [erro][ref_httpfailure] é manipulado normalmente com a captura da exceção e a exibição de informações adicionais sobre o erro.

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
        print("Error unauthorized. Make sure your key and endpoint are correct.")
    else:
        raise
```

### <a name="handle-transient-errors-with-retries"></a>Tratar erros transitórios com novas tentativas

Ao trabalhar com o cliente [ComputerVisionClient][ref_computervisionclient] , você pode encontrar falhas transitórias causadas por [limites de taxa][computervision_request_units] impostos pelo serviço ou outros problemas transitórios, como interrupções de rede. Para obter informações sobre como lidar com esses tipos de falhas, consulte [padrão de repetição][azure_pattern_retry] no guia de padrões de design de nuvem e o [padrão de disjuntor][azure_pattern_circuit_breaker]relacionado.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Aplicação de etiquetas de conteúdo a imagens](../concept-tagging-images.md)

<!-- LINKS -->
[pip]: https://pypi.org/project/pip/
[python]: https://www.python.org/downloads/

[azure_cli]: https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create
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


[computervision_resource]: https://azure.microsoft.com/try/cognitive-services/?

[computervision_docs]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/home

[ref_computervisionclient]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_analyze_image]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_list_models]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_analyze_image_by_domain]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_describe_image]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_get_text_operation_result]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_generate_thumbnail]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python


[ref_computervision_model_visualfeatures]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python

[ref_computervision_model_textoperationstatuscodes]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.textoperationstatuscodes?view=azure-python

[computervision_request_units]:https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/
