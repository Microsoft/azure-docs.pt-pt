---
title: 'Quickstart: Síntese assíncronea para áudio de forma longa (Preview) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Utilize a API de Áudio Longo para converter assincroticamente texto em voz e recuperar a saída de áudio de um URI fornecido pelo serviço. Esta API REST é ideal para fornecedores de conteúdos que precisam converter ficheiros de texto superiores a 10.000 caracteres ou 50 parágrafos em discurso sintetizado.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.custom: devx-track-python
ms.openlocfilehash: 330bd3593ea10e4a4a03e430025b8acf1f7ab21a
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87875392"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>Quickstart: Síntese assíncronea para áudio de forma longa em Python (Preview)

Neste arranque rápido, utilizará a API de Áudio Longo para converter assíncronamente o texto em linguagem e recuperará a saída de áudio de um URI fornecido pelo serviço. Esta API REST é ideal para fornecedores de conteúdo que precisam de sintetizar áudio a partir de texto superior a 5.000 caracteres (ou mais de 10 minutos de comprimento). Para mais informações, consulte [API de Áudio Longo.](../../long-audio-api.md)

A síntese assíncronea para áudio de forma longa pode ser usada com [vozes neurais públicas](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices) e [vozes neurais personalizadas,](../../how-to-custom-voice.md#custom-neural-voices)cada uma das quais suporta uma linguagem e dialeto específicos. 

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Python 2.7.x ou 3.x.
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code,](https://code.visualstudio.com/download)ou o seu editor de texto favorito.
* Uma assinatura Azure e uma chave de subscrição de serviço de discurso. [Crie uma conta Azure](../../get-started.md#new-resource) e [crie um recurso](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-resource) de fala para obter a chave. Ao criar o recurso Speech, certifique-se de que o seu nível de preços está definido para **S0,** e a localização está definida para uma [região suportada](../../regions.md#standard-and-neural-voices).

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos exigidos

Crie um novo projeto do Python através do seu editor ou IDE favorito. Em seguida, copie este código snippet em um ficheiro chamado `voice_synthesis_client.py` .

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
> Se não utilizou estes módulos, terá de os instalar antes de executar o seu programa. Para instalar estes pacotes, execute: `pip install requests urllib3`.

Estes módulos são usados para analisar argumentos, construir o pedido HTTP, e chamar a API de áudio longo de texto para discurso.

## <a name="get-a-list-of-supported-voices"></a>Obtenha uma lista de vozes apoiadas

Este código permite-lhe obter uma lista completa de vozes para uma região/ponto final específico que pode usar. Verifique a [região/ponto final suportado.](../../long-audio-api.md) Adicione o código `voice_synthesis_client.py` para:

```python
parser = argparse.ArgumentParser(description='Text-to-speech client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the speech subscription key, like fg1f763i01d94768bda32u7a******** ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0-beta1/' % args.region

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

* `<your_key>`Substitua-a pela tecla de subscrição do serviço Desema. Estas informações estão disponíveis no **separador Visão Geral** para o seu recurso no [portal Azure.](https://aka.ms/azureportal)
* `<region>`Substitua-a pela região onde foi criado o seu recurso Speech (por exemplo: `eastus` ou `westus` ). Estas informações estão disponíveis no **separador Visão Geral** para o seu recurso no [portal Azure.](https://aka.ms/azureportal)

Execute este comando:

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

Verá uma saída parecida com esta:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

Se o parâmetro **PublicVoice** é **Verdadeiro,** a voz é voz neural pública. Caso contrário, é uma voz neural personalizada. 

## <a name="prepare-input-files"></a>Preparar ficheiros de entrada

Prepare um ficheiro de texto de entrada. Pode ser texto simples ou texto SSML. Para os requisitos do ficheiro de entrada, consulte como [preparar o conteúdo para a síntese](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#prepare-content-for-synthesis).

## <a name="convert-text-to-speech"></a>Converter texto em discurso

Depois de preparar o ficheiro de texto de entrada, adicione este código para a síntese da fala `voice_synthesis_client.py` para:

> [!NOTE]
> 'concatenateResult' é um parâmetro opcional. Se este parâmetro não estiver definido, as saídas de áudio serão geradas por parágrafo. Também pode concatenar os áudios em 1 saída definindo o parâmetro. Por predefinição, a saída de áudio é definida para riff-16khz-16bit-mono-pcm. Para obter mais informações sobre saídas de áudio suportadas, consulte [os formatos de saída áudio](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#audio-output-formats).

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

* `<your_key>`Substitua-a pela tecla de subscrição do serviço Desema. Estas informações estão disponíveis no **separador Visão Geral** para o seu recurso no [portal Azure.](https://aka.ms/azureportal)
* `<region>`Substitua-a pela região onde foi criado o seu recurso Speech (por exemplo: `eastus` ou `westus` ). Estas informações estão disponíveis no **separador Visão Geral** para o seu recurso no [portal Azure.](https://aka.ms/azureportal)
* `<input>`Substitua-o pelo caminho para o ficheiro de texto que preparou para o texto-a-voz.
* `<locale>`Substitua-a pelo local de saída pretendido. Para mais informações, consulte [o suporte linguístico.](../../language-support.md#neural-voices)
* `<voice_guid>`Substitua-a pela voz de saída desejada. Utilize uma das vozes devolvidas por [Obter uma lista de vozes apoiadas](#get-a-list-of-supported-voices).

Converter texto em discurso com este comando:

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> Se tiver mais de 1 ficheiros de entrada, terá de apresentar vários pedidos. Há algumas limitações que precisam de ser conscientes. 
> * O cliente está autorizado a submeter até **5** pedidos ao servidor por segundo para cada conta de subscrição do Azure. Se exceder a limitação, o cliente receberá um código de erro 429 (muitos pedidos). Por favor, reduza o valor do pedido por segundo
> * O servidor é autorizado a executar e fazer fila até **120** pedidos para cada conta de subscrição do Azure. Se exceder a limitação, o servidor devolverá um código de erro 429 (demasiados pedidos). Por favor, aguarde e evite submeter novo pedido até que alguns pedidos estejam concluídos

Verá uma saída parecida com esta:

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

O resultado contém o texto de entrada e os ficheiros de saída de áudio gerados pelo serviço. Pode descarregar estes ficheiros num fecho de correr.

## <a name="remove-previous-requests"></a>Remover pedidos anteriores

O servidor irá manter até **20.000** pedidos para cada conta de subscrição do Azure. Se o valor do seu pedido exceder esta limitação, por favor remova os pedidos anteriores antes de esvoaçar os novos. Se não remover os pedidos existentes, receberá uma notificação de erro.

Adicione o código `voice_synthesis_client.py` para:

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

Agora, vamos verificar quais os pedidos que já submeteu. Antes de continuar, terá de atualizar algumas coisas neste pedido:

* `<your_key>`Substitua-a pela tecla de subscrição do serviço Desema. Estas informações estão disponíveis no **separador Visão Geral** para o seu recurso no [portal Azure.](https://aka.ms/azureportal)
* `<region>`Substitua-a pela região onde foi criado o seu recurso Speech (por exemplo: `eastus` ou `westus` ). Estas informações estão disponíveis no **separador Visão Geral** para o seu recurso no [portal Azure.](https://aka.ms/azureportal)

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

* `<your_key>`Substitua-a pela tecla de subscrição do serviço Desema. Estas informações estão disponíveis no **separador Visão Geral** para o seu recurso no [portal Azure.](https://aka.ms/azureportal)
* `<region>`Substitua-a pela região onde foi criado o seu recurso Speech (por exemplo: `eastus` ou `westus` ). Estas informações estão disponíveis no **separador Visão Geral** para o seu recurso no [portal Azure.](https://aka.ms/azureportal)
* `<synthesis_id>`Substitua-o pelo valor devolvido no pedido anterior.

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

## <a name="get-the-full-client"></a>Obtenha o cliente completo

O concluído `voice_synthesis_client.py` está disponível para download no [GitHub.](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre a API de Áudio Longo](../../long-audio-api.md)
