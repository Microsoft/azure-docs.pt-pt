---
title: 'Quickstart: Síntese assíncrona para áudio de forma longa (Pré-visualização) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Utilize a API de áudio longo para converter sincronicamente texto em discurso e recuperar a saída de áudio de um URI fornecido pelo serviço. Esta API REST é ideal para fornecedores de conteúdos que precisam converter ficheiros de texto superiores a 10.000 caracteres ou 50 parágrafos em discurso sintetizado.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: d3cd330001bcf53e7bd4fb9e6955c76a9ef20511
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331081"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>Quickstart: Síntese assíncrona para áudio de longa forma em Python (Pré-visualização)

Neste arranque rápido, utilizará a API de Áudio Longo para converter sincronicamente texto em discurso e recuperar a saída de áudio de um URI fornecido pelo serviço. Esta API REST é ideal para fornecedores de conteúdos que precisam de sintetizar áudio a partir de texto superior a 5.000 caracteres (ou mais de 10 minutos de comprimento). Para mais informações, consulte [Long Audio API](../../long-audio-api.md).

> [!NOTE]
> A síntese assíncrona para áudio de forma longa só pode ser usada com [vozes neurais personalizadas](../../how-to-custom-voice.md#custom-neural-voices).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Python 2.7.x ou 3.x.
* [Estúdio Visual](https://visualstudio.microsoft.com/downloads/), [Código de Estúdio Visual,](https://code.visualstudio.com/download)ou o seu editor de texto favorito.
* Uma subscrição Azure e uma chave de subscrição do serviço Speech. [Crie uma conta Azure](../../get-started.md#new-resource) e [crie um recurso de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-resource) para obter a chave. Ao criar o recurso Speech, certifique-se de que o seu nível de preços está definido para **S0**, e a localização está definida para uma [região apoiada](../../regions.md#standard-and-neural-voices).

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos exigidos

Crie um novo projeto do Python através do seu editor ou IDE favorito. Em seguida, copie este fragmento `voice_synthesis_client.py`de código num ficheiro chamado .

```python
import argparse
import json
import ntpath
import urllib3
import requests
import time
from json import dumps, loads, JSONEncoder, JSONDecoder
import pickle

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
```

> [!NOTE]
> Se ainda não utilizou estes módulos, terá de os instalar antes de executar o seu programa. Para instalar estes pacotes, execute: `pip install requests urllib3`.

Estes módulos são usados para analisar argumentos, construir o pedido HTTP, e chamar o áudio longo de texto para a fala REST API.

## <a name="get-a-list-of-supported-voices"></a>Obtenha uma lista de vozes apoiadas

Este código obtém uma lista de vozes disponíveis que pode usar para converter texto-a-fala. Adicione o `voice_synthesis_client.py`código a:

```python
parser = argparse.ArgumentParser(description='Cris client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the cris subscription key, like ff1eb62d06d34767bda0207acb1da7d7 ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.cris.ai/api/texttospeech/v3.0-beta1/' % args.region

def getVoices():
    response=requests.get(baseAddress+"voicesynthesis/voices", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    voices = json.loads(response.text)
    return voices

if args.voices:
    voices = getVoices()
    print("There are %d voices available:" % len(voices))
    for voice in voices:
        print ("Name: %s, Description: %s, Id: %s, Locale: %s, Gender: %s, PublicVoice: %s, Created: %s" % (voice['name'], voice['description'], voice['id'], voice['locale'], voice['gender'], voice['isPublicVoice'], voice['created']))
```

### <a name="test-your-code"></a>Teste o seu código

Vamos testar o que fez até agora. Terá de atualizar algumas coisas no pedido abaixo:

* Substitua-a `<your_key>` com a chave de subscrição do serviço Speech. Esta informação está disponível no separador **Overview** para o seu recurso no [portal Azure](https://aka.ms/azureportal).
* Substitua-a `<region>` pela região onde o `eastus` seu `westus`recurso Speech foi criado (por exemplo: ou ). Esta informação está disponível no separador **Overview** para o seu recurso no [portal Azure](https://aka.ms/azureportal).

Execute este comando:

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

Verá uma saída que se parece com esta:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

## <a name="prepare-input-files"></a>Preparar ficheiros de entrada

Prepare um ficheiro de texto de entrada. Pode ser texto simples ou texto SSML. Para os requisitos de ficheiro de entrada, consulte como preparar o [conteúdo para a síntese](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#prepare-content-for-synthesis).

## <a name="convert-text-to-speech"></a>Converter texto em discurso

Depois de preparar o ficheiro de texto de `voice_synthesis_client.py`entrada, adicione este código de síntese da fala a:

> [!NOTE]
> 'concatenateResult' é um parâmetro opcional. Se este parâmetro não estiver definido, as saídas de áudio serão geradas por parágrafo. Também pode concatenar os áudios em 1 saída, definindo o parâmetro. Por predefinição, a saída de áudio está definida para riff-16khz-16bit-mono-pcm. Para obter mais informações sobre saídas de áudio suportadas, consulte [formatos](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#audio-output-formats)de saída áudio .

```python
parser.add_argument('--submit', action="store_true", default=False, help='submit a synthesis request')
parser.add_argument('--concatenateResult', action="store_true", default=False, help='If concatenate result in a single wave file')
parser.add_argument('-file', action="store", dest="file", help='the input text script file path')
parser.add_argument('-voiceId', action="store", nargs='+', dest="voiceId", help='the id of the voice which used to synthesis')
parser.add_argument('-locale', action="store", dest="locale", help='the locale information like zh-CN/en-US')
parser.add_argument('-format', action="store", dest="format", default='riff-16khz-16bit-mono-pcm', help='the output audio format')

def submitSynthesis():
    modelList = args.voiceId
    data={'name': 'simple test', 'description': 'desc...', 'models': json.dumps(modelList), 'locale': args.locale, 'outputformat': args.format}
    if args.concatenateResult:
        properties={'ConcatenateResult': 'true'}
        data['properties'] = json.dumps(properties)
    if args.file is not None:
        scriptfilename=ntpath.basename(args.file)
        files = {'script': (scriptfilename, open(args.file, 'rb'), 'text/plain')}
    response = requests.post(baseAddress+"voicesynthesis", data, headers={"Ocp-Apim-Subscription-Key":args.key}, files=files, verify=False)
    if response.status_code == 202:
        location = response.headers['Location']
        id = location.split("/")[-1]
        print("Submit synthesis request successful")
        return id
    else:
        print("Submit synthesis request failed")
        print("response.status_code: %d" % response.status_code)
        print("response.text: %s" % response.text)
        return 0

def getSubmittedSynthesis(id):
    response=requests.get(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    synthesis = json.loads(response.text)
    return synthesis

if args.submit:
    id = submitSynthesis()
    if (id == 0):
        exit(1)

    while(1):
        print("\r\nChecking status")
        synthesis=getSubmittedSynthesis(id)
        if synthesis['status'] == "Succeeded":
            r = requests.get(synthesis['resultsUrl'])
            filename=id + ".zip"
            with open(filename, 'wb') as f:  
                f.write(r.content)
                print("Succeeded... Result file downloaded : " + filename)
            break
        elif synthesis['status'] == "Failed":
            print("Failed...")
            break
        elif synthesis['status'] == "Running":
            print("Running...")
        elif synthesis['status'] == "NotStarted":
            print("NotStarted...")
        time.sleep(10)
```

### <a name="test-your-code"></a>Teste o seu código

Vamos fazer um pedido para sintetizar o texto usando o seu ficheiro de entrada como fonte. Terá de atualizar algumas coisas no pedido abaixo:

* Substitua-a `<your_key>` com a chave de subscrição do serviço Speech. Esta informação está disponível no separador **Overview** para o seu recurso no [portal Azure](https://aka.ms/azureportal).
* Substitua-a `<region>` pela região onde o `eastus` seu `westus`recurso Speech foi criado (por exemplo: ou ). Esta informação está disponível no separador **Overview** para o seu recurso no [portal Azure](https://aka.ms/azureportal).
* Substitua-o `<input>` pelo caminho para o ficheiro de texto que preparou para o texto-a-fala.
* Substitua-a `<locale>` pelo local de saída pretendido. Para mais informações, consulte o [suporte linguístico.](../../language-support.md#neural-voices)
* Substitua-a `<voice_guid>` com a voz de saída desejada. Use uma das vozes devolvidas por [Obter uma lista de vozes apoiadas](#get-a-list-of-supported-voices).

Converter texto para discurso com este comando:

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> Se tiver mais de 1 ficheiros de entrada, terá de submeter vários pedidos. Há algumas limitações que precisam de estar cientes. 
> * O cliente está autorizado a submeter até **5** pedidos ao servidor por segundo para cada conta de subscrição do Azure. Se exceder a limitação, o cliente receberá um código de erro 429 (pedidos a mais). Por favor, reduza o valor do pedido por segundo
> * O servidor está autorizado a executar e fazer fila até **120** pedidos para cada conta de subscrição do Azure. Se exceder a limitação, o servidor devolverá um código de erro 429 (pedidos a mais). Por favor, aguarde e evite submeter novo pedido até que alguns pedidos estejam concluídos

Verá uma saída que se parece com esta:

```console
Submit synthesis request successful

Checking status
NotStarted...

Checking status
Running...

Checking status
Running...

Checking status
Succeeded... Result file downloaded : xxxx.zip
```

O resultado contém o texto de entrada e os ficheiros de saída de áudio que são gerados pelo serviço. Pode descarregar estes ficheiros num fecho.

## <a name="remove-previous-requests"></a>Remover pedidos anteriores

O servidor manterá até **20.000** pedidos para cada conta de subscrição do Azure. Se o seu valor de pedido exceder esta limitação, remova os pedidos anteriores antes de efazer novos. Se não remover os pedidos existentes, receberá uma notificação de erro.

Adicione o `voice_synthesis_client.py`código a:

```python
parser.add_argument('--syntheses', action="store_true", default=False, help='print synthesis list')
parser.add_argument('--delete', action="store_true", default=False, help='delete a synthesis request')
parser.add_argument('-synthesisId', action="store", nargs='+', dest="synthesisId", help='the id of the voice synthesis which need to be deleted')

def getSubmittedSyntheses():
    response=requests.get(baseAddress+"voicesynthesis", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    syntheses = json.loads(response.text)
    return syntheses

def deleteSynthesis(ids):
    for id in ids:
        print("delete voice synthesis %s " % id)
        response = requests.delete(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
        if (response.status_code == 204):
            print("delete successful")
        else:
            print("delete failed, response.status_code: %d, response.text: %s " % (response.status_code, response.text))

if args.syntheses:
    synthese = getSubmittedSyntheses()
    print("There are %d synthesis requests submitted:" % len(synthese))
    for synthesis in synthese:
        print ("ID : %s , Name : %s, Status : %s " % (synthesis['id'], synthesis['name'], synthesis['status']))

if args.delete:
    deleteSynthesis(args.synthesisId)
```

### <a name="test-your-code"></a>Teste o seu código

Agora, vamos verificar os pedidos que já submeteu. Antes de continuar, terá de atualizar algumas coisas neste pedido:

* Substitua-a `<your_key>` com a chave de subscrição do serviço Speech. Esta informação está disponível no separador **Overview** para o seu recurso no [portal Azure](https://aka.ms/azureportal).
* Substitua-a `<region>` pela região onde o `eastus` seu `westus`recurso Speech foi criado (por exemplo: ou ). Esta informação está disponível no separador **Overview** para o seu recurso no [portal Azure](https://aka.ms/azureportal).

Execute este comando:

```console
python voice_synthesis_client.py --syntheses -key <your_key> -region <Region>
```

Isto devolverá uma lista de pedidos de síntese que fez. Verá uma saída como esta:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Agora, vamos remover um pedido previamente apresentado. Terá de atualizar algumas coisas no código abaixo:

* Substitua-a `<your_key>` com a chave de subscrição do serviço Speech. Esta informação está disponível no separador **Overview** para o seu recurso no [portal Azure](https://aka.ms/azureportal).
* Substitua-a `<region>` pela região onde o `eastus` seu `westus`recurso Speech foi criado (por exemplo: ou ). Esta informação está disponível no separador **Overview** para o seu recurso no [portal Azure](https://aka.ms/azureportal).
* Substitua `<synthesis_id>` pelo valor devolvido no pedido anterior.

> [!NOTE]
> Os pedidos com estatuto de 'Running'/'Waiting' não podem ser removidos ou eliminados.

Execute este comando:

```console
python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

Verá uma saída como esta:

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>Obter o cliente completo

O completo `voice_synthesis_client.py` está disponível para download no [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre a API de Áudio Longo](../../long-audio-api.md)
