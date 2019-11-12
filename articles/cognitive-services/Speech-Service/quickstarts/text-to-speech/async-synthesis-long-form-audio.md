---
title: 'Início rápido: síntese assíncrona para áudio de forma longa (versão prévia) – serviço de fala'
titleSuffix: Azure Cognitive Services
description: Use a API de áudio longo para converter de forma assíncrona o texto em fala e recupere a saída de áudio de um URI fornecido pelo serviço. Essa API REST é ideal para provedores de conteúdo que precisam converter arquivos de texto com mais de 10.000 caracteres ou 50 parágrafos em fala sintetizada.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: d308623a323fecd39efa90639da71fb981936fe5
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930585"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>Início rápido: síntese assíncrona para áudio de forma longa em Python (versão prévia)

Neste guia de início rápido, você usará a API de áudio longo para converter de forma assíncrona o texto em fala e recuperará a saída de áudio de um URI fornecido pelo serviço. Essa API REST é ideal para provedores de conteúdo que precisam converter arquivos de texto com mais de 10.000 caracteres ou 50 parágrafos em fala sintetizada. Para obter mais informações, consulte [longa API de áudio](../../long-audio-api.md).

> [!NOTE]
> A síntese assíncrona para áudio de forma longa só pode ser usada com [vozes neurais personalizadas](../../how-to-custom-voice.md#custom-neural-voices).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Python 2.7. x ou 3. x.
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download)ou seu editor de texto favorito.
* Uma assinatura do Azure e uma chave de assinatura do serviço de fala. [Crie uma conta do Azure](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-azure-account) e [crie um recurso de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure) para obter a chave. Ao criar o recurso de fala, verifique se o tipo de preço está definido como **S0**e se o local está definido como uma [região com suporte](../../regions.md#standard-and-neural-voices).

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos exigidos

Crie um novo projeto do Python através do seu editor ou IDE favorito. Em seguida, copie esse trecho de código em um arquivo chamado `voice_synthesis_client.py`.

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
> Se ainda não utilizou estes módulos, terá de instalá-los antes de executar o seu programa. Para instalar estes pacotes, execute: `pip install requests urllib3`.

Esses módulos são usados para analisar argumentos, construir a solicitação HTTP e chamar a API REST de áudio longo de conversão de texto em fala.

## <a name="get-a-list-of-supported-voices"></a>Obter uma lista de vozes com suporte

Esse código obtém uma lista de vozes disponíveis que você pode usar para converter conversão de texto em fala. Adicione este código `voice_synthesis_client.py`:

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

### <a name="test-your-code"></a>Testar seu código

Vamos testar o que você fez até agora. Execute este comando, substituindo `<your_key>` pela sua chave de assinatura de fala e `<region>` pela região em que o recurso de fala foi criado (por exemplo: `eastus` ou `westus`). Essas informações estão disponíveis na guia **visão geral** do recurso na [portal do Azure](https://aka.ms/azureportal).

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

Você deve obter uma saída parecida com esta:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

## <a name="convert-text-to-speech"></a>Converter texto em fala

A próxima etapa é preparar um arquivo de texto de entrada. Pode ser texto sem formatação ou SSML, mas deve ter mais de 10.000 caracteres ou 50 parágrafos. Para obter uma lista completa dos requisitos, consulte [API de áudio de longa duração](../../long-audio-api.md).

Depois de preparar o arquivo de texto. A próxima etapa é adicionar o código para a síntese de fala ao seu projeto. Adicione este código a `voice_synthesis_client.py`:

> [!NOTE]
> Por padrão, a saída de áudio é definida como riff-16kHz-16 bits-mono-PCM. Para obter mais informações sobre as saídas de áudio com suporte, consulte [longa API de áudio](../../long-audio-api.md#audio-output-formats).

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
        location = response.headers['Operation-Location']
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

### <a name="test-your-code"></a>Testar seu código

Vamos tentar fazer uma solicitação para sintetizar o texto usando seu arquivo de entrada como fonte. Você precisará atualizar algumas coisas na solicitação abaixo:

* Substitua `<your_key>` pela sua chave de assinatura do serviço de fala. Essas informações estão disponíveis na guia **visão geral** do recurso na [portal do Azure](https://aka.ms/azureportal).
* Substitua `<region>` pela região em que o recurso de fala foi criado (por exemplo: `eastus` ou `westus`). Essas informações estão disponíveis na guia **visão geral** do recurso na [portal do Azure](https://aka.ms/azureportal).
* Substitua `<input>` pelo caminho para o arquivo de texto que você pretende converter de conversão de texto em fala.
* Substitua `<locale>` pela localidade de saída desejada. Para obter mais informações, consulte [suporte a idiomas](../../language-support.md#neural-voices).
* Substitua `<voice_guid>` pela voz desejada para a saída de áudio. Use uma das vozes retornadas por [obter uma lista de vozes com suporte](#get-a-list-of-supported-voices) ou use a lista de vozes neurais fornecidas no [suporte a idiomas](../../language-support.md#neural-voices).

Converter texto em fala com este comando:

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> ' concatenateResult ' é um parâmetro opcional, se esse parâmetro não for fornecido, a saída será fornecida como vários arquivos Wave, um para cada linha.

Você deve obter uma saída parecida com esta:

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

O resultado fornecido contém o texto de entrada e os arquivos de saída de áudio gerados pelo serviço. Eles são baixados como um zip.

## <a name="remove-previous-requests"></a>Remover solicitações anteriores

Há um limite de 2.000 solicitações para cada assinatura. Assim, haverá ocasiões em que você precisa remover as solicitações enviadas anteriormente antes de poder fazer novas. Se você não remover as solicitações existentes, receberá um erro quando exceder 2.000.

Adicione este código a `voice_synthesis_client.py`:

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

### <a name="test-your-code"></a>Testar seu código

Execute este comando, substituindo `<your_key>` pela sua chave de assinatura de fala e `<region>` pela região em que o recurso de fala foi criado (por exemplo: `eastus` ou `westus`). Essas informações estão disponíveis na guia **visão geral** do recurso na [portal do Azure](https://aka.ms/azureportal).

```console
python voice_synthesis_client.py – syntheses -key <your_key> -region <Region>
```

Isso retornará uma lista de Syntheses que você solicitou. Você deve obter uma saída parecida com esta:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Agora, vamos usar alguns desses valores para remover/excluir solicitações enviadas anteriormente. Execute este comando, substituindo `<your_key>` pela sua chave de assinatura de fala e `<region>` pela região em que o recurso de fala foi criado (por exemplo: `eastus` ou `westus`). Essas informações estão disponíveis na guia **visão geral** do recurso na [portal do Azure](https://aka.ms/azureportal). O `<synthesis_id>` deve ser um dos valores retornados na solicitação anterior.

> [!NOTE]
> As solicitações com o status ' running '/' Wait ' não podem ser removidas ou excluídas.

```console
python voice_synthesis_client.py – delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

Você deve obter uma saída parecida com esta:

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>Obter o cliente completo

O `voice_synthesis_client.py` completo está disponível para download no [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre a API de áudio longo](../../long-audio-api.md)
