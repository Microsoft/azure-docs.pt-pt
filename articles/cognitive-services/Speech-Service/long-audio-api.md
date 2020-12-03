---
title: API de áudio longo (pré-visualização) - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como a API de áudio longo é projetada para síntese assíncrono de texto de forma longa para a fala.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: 255cfe11f8601abc89a1d96f702f453c2af1ccbd
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533065"
---
# <a name="long-audio-api-preview"></a>API de áudio longo (pré-visualização)

A API de Áudio Longo foi concebida para uma síntese assíncrono de texto de longa forma para a fala (por exemplo: livros de áudio, artigos de notícias e documentos). Esta API não devolve áudio sintetizado em tempo real, em vez disso a expectativa é que você vai pesquisar para a(s) resposta(s) e consumir a(s) output(s) como eles são disponibilizados a partir do serviço. Ao contrário do texto para falar API que é usado pelo Speech SDK, a API de Áudio Longo pode criar áudio sintetizado por mais de 10 minutos, tornando-o ideal para editores e plataformas de conteúdos áudio.

Benefícios adicionais da API de Áudio Longo:

* O discurso sintetizado devolvido pelo serviço usa as melhores vozes neurais.
* Não há necessidade de implantar um ponto final de voz, uma vez que sintetiza as vozes em nenhum modo de lote em tempo real.

> [!NOTE]
> A API de Áudio Longo suporta agora [vozes neurais públicas](./language-support.md#neural-voices) e [vozes neuronais personalizadas.](./how-to-custom-voice.md#custom-neural-voices)

## <a name="workflow"></a>Fluxo de trabalho

Normalmente, quando utilizar a API de Áudio Longo, irá submeter um ficheiro de texto ou ficheiros a serem sintetizados, fazer uma sondagem para o estado, então se o estado for bem sucedido, pode descarregar a saída de áudio.

Este diagrama fornece uma visão geral de alto nível do fluxo de trabalho.

![Longo diagrama de fluxo de trabalho da API áudio](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Preparar conteúdo para a síntese

Ao preparar o seu ficheiro de texto, certifique-se de que:

* Ou é texto simples (.txt) ou texto SSML (.txt)
* Está codificado como [UTF-8 com Byte Order Mark (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* É um único ficheiro, não um fecho
* Contém mais de 400 caracteres para texto simples ou 400 [caracteres faturantes](./text-to-speech.md#pricing-note) para texto SSML, e menos de 10.000 parágrafos
  * Para texto simples, cada parágrafo é separado por bater **Enter/Return** - Ver [exemplo de entrada de texto simples](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * Para o texto SSML, cada peça SSML é considerada um parágrafo. As peças SSML devem ser separadas por parágrafos diferentes - Ver [exemplo de entrada de texto SSML](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> Para os chineses (continente), chineses (HONG Kong SAR), chineses (Taiwan), japoneses e coreanos, uma palavra será contada como dois caracteres. 

## <a name="python-example"></a>Exemplo python

Esta secção contém exemplos python que mostram o uso básico da API de Áudio Longo. Crie um novo projeto do Python através do seu editor ou IDE favorito. Em seguida, copie este código snippet em um ficheiro chamado `voice_synthesis_client.py` .

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

Estas bibliotecas são usadas para analisar argumentos, construir o pedido HTTP, e chamar a API de áudio longo de texto para discurso.

### <a name="get-a-list-of-supported-voices"></a>Obtenha uma lista de vozes apoiadas

Este código permite-lhe obter uma lista completa de vozes para uma região/ponto final específico que pode usar. Adicione o código `voice_synthesis_client.py` para:

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

Executar o script usando o comando `python voice_synthesis_client.py --voices -key <your_key> -region <region>` e substituir os seguintes valores:

* `<your_key>`Substitua-a pela tecla de subscrição do serviço Desema. Estas informações estão disponíveis no **separador Visão Geral** para o seu recurso no [portal Azure.](https://aka.ms/azureportal)
* `<region>`Substitua-a pela região onde foi criado o seu recurso Speech (por exemplo: `eastus` ou `westus` ). Estas informações estão disponíveis no **separador Visão Geral** para o seu recurso no [portal Azure.](https://aka.ms/azureportal)

Verá uma saída parecida com esta:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

Se o parâmetro **PublicVoice** é **Verdadeiro,** a voz é voz neural pública. Caso contrário, é uma voz neural personalizada.

### <a name="convert-text-to-speech"></a>Converter texto em discurso

Prepare um ficheiro de texto de entrada, em texto simples ou em texto SSML, em seguida, adicione o seguinte código `voice_synthesis_client.py` para:

> [!NOTE]
> 'concatenateResult' é um parâmetro opcional. Se este parâmetro não estiver definido, as saídas de áudio serão geradas por parágrafo. Também pode concatenar os áudios em 1 saída definindo o parâmetro. Por predefinição, a saída de áudio é definida para riff-16khz-16bit-mono-pcm. Para obter mais informações sobre saídas de áudio suportadas, consulte [os formatos de saída áudio](#audio-output-formats).

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

Executar o script usando o comando `python voice_synthesis_client.py --submit -key <your_key> -region <region> -file <input> -locale <locale> -voiceId <voice_guid>` e substituir os seguintes valores:

* `<your_key>`Substitua-a pela tecla de subscrição do serviço Desema. Estas informações estão disponíveis no **separador Visão Geral** para o seu recurso no [portal Azure.](https://aka.ms/azureportal)
* `<region>`Substitua-a pela região onde foi criado o seu recurso Speech (por exemplo: `eastus` ou `westus` ). Estas informações estão disponíveis no **separador Visão Geral** para o seu recurso no [portal Azure.](https://aka.ms/azureportal)
* `<input>`Substitua-o pelo caminho para o ficheiro de texto que preparou para o texto-a-voz.
* `<locale>`Substitua-a pelo local de saída pretendido. Para mais informações, consulte [o suporte linguístico.](language-support.md#neural-voices)
* `<voice_guid>`Substitua-a pela voz de saída desejada. Utilize uma das vozes devolvidas pela sua chamada anterior ao `/voicesynthesis/voices` ponto final.

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

> [!NOTE]
> Se tiver mais de 1 ficheiros de entrada, terá de apresentar vários pedidos. Há algumas limitações que precisam de ser conscientes. 
> * O cliente está autorizado a submeter até **5** pedidos ao servidor por segundo para cada conta de subscrição do Azure. Se exceder a limitação, o cliente receberá um código de erro 429 (muitos pedidos). Por favor, reduza o valor do pedido por segundo
> * O servidor é autorizado a executar e fazer fila até **120** pedidos para cada conta de subscrição do Azure. Se exceder a limitação, o servidor devolverá um código de erro 429 (demasiados pedidos). Por favor, aguarde e evite submeter novo pedido até que alguns pedidos estejam concluídos

### <a name="remove-previous-requests"></a>Remover pedidos anteriores

O serviço irá manter até **20.000** pedidos para cada conta de subscrição da Azure. Se o valor do seu pedido exceder esta limitação, por favor remova os pedidos anteriores antes de esvoaçar os novos. Se não remover os pedidos existentes, receberá uma notificação de erro.

Adicione o seguinte código a `voice_synthesis_client.py`:

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

Corra `python voice_synthesis_client.py --syntheses -key <your_key> -region <region>` para obter uma lista de pedidos de síntese que fez. Verá uma saída como esta:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Para eliminar um pedido, executar `python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>` e substituir por um valor de `<synthesis_id>` ID de pedido devolvido do pedido anterior.

> [!NOTE]
> Os pedidos com estatuto de 'Running'/'Waiting' não podem ser removidos ou eliminados.

O concluído `voice_synthesis_client.py` está disponível no [GitHub.](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py)

## <a name="http-status-codes"></a>Códigos de estado HTTP

A tabela seguinte detalha os códigos e mensagens de resposta HTTP da API REST.

| API | Código de estado de HTTP | Descrição | Solução |
|-----|------------------|-------------|----------|
| Criar | 400 | A síntese de voz não está ativada nesta região. | Mude a chave de subscrição de discursos com uma região apoiada. |
|        | 400 | Apenas a subscrição de discurso **padrão** para esta região é válida. | Mude a chave de subscrição de discurso para o nível de preços "Standard". |
|        | 400 | Exceda o limite de 20.000 pedidos para a conta Azure. Por favor, remova alguns pedidos antes de enviar novos. | O servidor irá manter até 20.000 pedidos para cada conta Azure. Elimine alguns pedidos antes de enviar novos. |
|        | 400 | Este modelo não pode ser utilizado na síntese de voz : {modelID}. | Certifique-se de que o estado de {modelID}está correto. |
|        | 400 | A região para o pedido não corresponde à região para o modelo : {modelID}. | Certifique-se de que a região {modelID}corresponde à região do pedido. |
|        | 400 | A síntese de voz só suporta o ficheiro de texto na codificação UTF-8 com o marcador byte-order. | Certifique-se de que os ficheiros de entrada estão na codificação UTF-8 com o marcador de encomenda. |
|        | 400 | Apenas entradas SSML válidas são permitidas no pedido de síntese de voz. | Certifique-se de que as expressões SSML de entrada estão corretas. |
|        | 400 | O nome de voz {voiceName} não se encontra no ficheiro de entrada. | O nome de voz SSML de entrada não está alinhado com o ID do modelo. |
|        | 400 | O número de parágrafos no ficheiro de entrada deve ser inferior a 10.000. | Certifique-se de que o número de parágrafos no ficheiro é inferior a 10.000. |
|        | 400 | O ficheiro de entrada deve ter mais de 400 caracteres. | Certifique-se de que o seu ficheiro de entrada excede 400 caracteres. |
|        | 404 | O modelo declarado na definição de síntese de voz não pode ser encontrado : {modelID}. | Certifique-se de que o {modelID} está correto. |
|        | 429 | Ultrapasse o limite de síntese de voz ativa. Por favor, espere até que alguns pedidos terminem. | O servidor é autorizado a executar e fazer fila até 120 pedidos para cada conta Azure. Por favor, aguarde e evite submeter novos pedidos até que alguns pedidos estejam concluídos. |
| Todos       | 429 | Há muitos pedidos. | O cliente está autorizado a submeter até 5 pedidos ao servidor por segundo para cada conta Azure. Por favor, reduza o valor do pedido por segundo. |
| Eliminar    | 400 | A tarefa de síntese de voz ainda está em uso. | Só é possível eliminar pedidos **que sejam preenchidos** ou **falhados**. |
| GetByID   | 404 | A entidade especificada não pode ser encontrada. | Certifique-se de que o ID da síntese está correto. |

## <a name="regions-and-endpoints"></a>Regiões e pontos finais

A API áudio longa está disponível em várias regiões com pontos finais únicos.

| Região | Ponto final |
|--------|----------|
| E.U.A. Leste | `https://eastus.customvoice.api.speech.microsoft.com` |
| Índia Central | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Sudeste Asiático | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Sul do Reino Unido | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Europa Ocidental | `https://westeurope.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>Formatos de saída de áudio

Apoiamos formatos flexíveis de saída de áudio. Pode gerar saídas de áudio por parágrafo ou concatenar as saídas de áudio numa única saída, definindo o parâmetro "concatenateResult". Os seguintes formatos de saída de áudio são suportados pela API de Áudio Longo:

> [!NOTE]
> O formato áudio predefinido é riff-16khz-16bit-mono-pcm.

* riff-8khz-16bit-mono-pcm
* riff-16khz-16bit-mono-pcm
* riff-24khz-16bit-mono-pcm
* riff-48khz-16bit-mono-pcm
* áudio-16khz-32kbitrate-mono-mp3
* áudio-16khz-64kbitrate-mono-mp3
* áudio-16khz-128kbitrate-mono-mp3
* áudio-24khz-48kbitrate-mono-mp3
* audio-24khz-96kbitrate-mono-mp3
* audio-24khz-160kbitrate-mono-mp3

## <a name="sample-code"></a>Código de exemplo
O código de amostra para API de áudio longo está disponível no GitHub.

* [Código de amostra: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Código de amostra: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Código de amostra: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)