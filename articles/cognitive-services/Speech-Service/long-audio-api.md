---
title: API de Áudio Longo - Serviço de Fala
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
ms.openlocfilehash: e28bd5b5caca259201758f0c633b2120a411f422
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007453"
---
# <a name="long-audio-api"></a>API de áudio longo

A API de Áudio Longo foi concebida para uma síntese assíncrono de texto de longa forma para a fala (por exemplo: livros de áudio, artigos de notícias e documentos). Esta API não devolve áudio sintetizado em tempo real, em vez disso a expectativa é que você vai pesquisar para a(s) resposta(s) e consumir a(s) output(s) como eles são disponibilizados a partir do serviço. Ao contrário do texto para falar API que é usado pelo Speech SDK, a API de Áudio Longo pode criar áudio sintetizado por mais de 10 minutos, tornando-o ideal para editores e plataformas de conteúdo sonoro criarem longos conteúdos áudio como livros de áudio num lote.

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

## <a name="python-example"></a>Exemplo python

Esta secção contém exemplos python que mostram o uso básico da API de Áudio Longo. Crie um novo projeto do Python através do seu editor ou IDE favorito. Em seguida, copie este código snippet em um ficheiro chamado `long_audio_synthesis_client.py` .

```python
import json
import ntpath
import requests
```

Estas bibliotecas são usadas para construir o pedido HTTP, e chamam a síntese de áudio de texto-a-fala REST API.

### <a name="get-a-list-of-supported-voices"></a>Obtenha uma lista de vozes apoiadas

Para obter uma lista de vozes apoiadas, envie um pedido GET para `https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/voices` .


Este código permite-lhe obter uma lista completa de vozes para uma região/ponto final específico que pode usar.
```python
def get_voices():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/voices'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print(response.text)

get_voices()
```

Substitua os seguintes valores:

* `<your_key>`Substitua-a pela tecla de subscrição do serviço Desema. Estas informações estão disponíveis no **separador Visão Geral** para o seu recurso no [portal Azure.](https://aka.ms/azureportal)
* `<region>`Substitua-a pela região onde foi criado o seu recurso Speech (por exemplo: `eastus` ou `westus` ). Estas informações estão disponíveis no **separador Visão Geral** para o seu recurso no [portal Azure.](https://aka.ms/azureportal)

Verá uma saída parecida com esta:

```console
{
  "values": [
    {
      "locale": "en-US",
      "voiceName": "en-US-AriaNeural",
      "description": "",
      "gender": "Female",
      "createdDateTime": "2020-05-21T05:57:39.123Z",
      "properties": {
        "publicAvailable": true
      }
    },
    {
      "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141"
      "locale": "en-US",
      "voiceName": "my custom neural voice",
      "description": "",
      "gender": "Male",
      "createdDateTime": "2020-05-21T05:25:40.243Z",
      "properties": {
        "publicAvailable": false
      }
    }
  ]
}
```

Se **propriedades.publiciu a disponível** é **verdadeira,** a voz é uma voz neural pública. Caso contrário, é uma voz neural personalizada.

### <a name="convert-text-to-speech"></a>Converter texto em discurso

Prepare um ficheiro de texto de entrada, em texto simples ou em texto SSML, em seguida, adicione o seguinte código `long_audio_synthesis_client.py` para:

> [!NOTE]
> `concatenateResult` é um parâmetro opcional. Se este parâmetro não estiver definido, as saídas de áudio serão geradas por parágrafo. Também pode concatenar os áudios em 1 saída definindo o parâmetro. 
> `outputFormat` também é opcional. Por predefinição, a saída de áudio é definida para riff-16khz-16bit-mono-pcm. Para obter mais informações sobre formatos de saída de áudio suportados, consulte [os formatos de saída áudio](#audio-output-formats).

```python
def submit_synthesis():
    region = '<region>'
    key = '<your_key>'
    input_file_path = '<input_file_path>'
    locale = '<locale>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    voice_identities = [
        {
            'voicename': '<voice_name>'
        }
    ]

    payload = {
        'displayname': 'long audio synthesis sample',
        'description': 'sample description',
        'locale': locale,
        'voices': json.dumps(voice_identities),
        'outputformat': 'riff-16khz-16bit-mono-pcm',
        'concatenateresult': True,
    }

    filename = ntpath.basename(input_file_path)
    files = {
        'script': (filename, open(input_file_path, 'rb'), 'text/plain')
    }

    response = requests.post(url, payload, headers=header, files=files)
    print('response.status_code: %d' % response.status_code)
    print(response.headers['Location'])

submit_synthesis()
```

Substitua os seguintes valores:

* `<your_key>`Substitua-a pela tecla de subscrição do serviço Desema. Estas informações estão disponíveis no **separador Visão Geral** para o seu recurso no [portal Azure.](https://aka.ms/azureportal)
* `<region>`Substitua-a pela região onde foi criado o seu recurso Speech (por exemplo: `eastus` ou `westus` ). Estas informações estão disponíveis no **separador Visão Geral** para o seu recurso no [portal Azure.](https://aka.ms/azureportal)
* `<input_file_path>`Substitua-o pelo caminho para o ficheiro de texto que preparou para o texto-a-voz.
* `<locale>`Substitua-a pelo local de saída pretendido. Para mais informações, consulte [o suporte linguístico.](language-support.md#neural-voices)

Utilize uma das vozes devolvidas pela sua chamada anterior ao `/voices` ponto final.

* Se estiver a utilizar a voz neural pública, `<voice_name>` substitua-a pela voz de saída desejada.
* Para utilizar uma voz neural personalizada, substitua `voice_identities` a variável por seguinte e substitua-a `<voice_id>` pela voz neural `id` personalizada.
```Python
voice_identities = [
    {
        'id': '<voice_id>'
    }
]
```

Verá uma saída parecida com esta:

```console
response.status_code: 202
https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/<guid>
```

> [!NOTE]
> Se tiver mais de 1 ficheiros de entrada, terá de apresentar vários pedidos. Há algumas limitações que precisam de ser conscientes.
> * O cliente está autorizado a submeter até **5** pedidos ao servidor por segundo para cada conta de subscrição do Azure. Se exceder a limitação, o cliente receberá um código de erro 429 (muitos pedidos). Por favor, reduza o valor do pedido por segundo.
> * O servidor é autorizado a executar e fazer fila até **120** pedidos para cada conta de subscrição do Azure. Se exceder a limitação, o servidor devolverá um código de erro 429 (demasiados pedidos). Por favor, aguarde e evite submeter novo pedido até que alguns pedidos estejam concluídos.

O URL na saída pode ser usado para obter o estado do pedido.

### <a name="get-information-of-a-submitted-request"></a>Obter informações de um pedido submetido

Para obter o estado de um pedido de síntese submetido, basta enviar um pedido GET para o URL devolvido por etapa anterior.
```Python

def get_synthesis():
    url = '<url>'
    key = '<your_key>'
    header = {
        'Ocp-Apim-Subscription-Key': key
    }
    response = requests.get(url, headers=header)
    print(response.text)

get_synthesis()
```
A saída será assim:
```console
response.status_code: 200
{
  "models": [
    {
      "voiceName": "en-US-AriaNeural"
    }
  ],
  "properties": {
    "outputFormat": "riff-16khz-16bit-mono-pcm",
    "concatenateResult": false,
    "totalDuration": "PT5M57.252S",
    "billableCharacterCount": 3048
  },
  "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
  "lastActionDateTime": "2021-01-14T11:12:27.240Z",
  "status": "Succeeded",
  "createdDateTime": "2021-01-14T11:11:02.557Z",
  "locale": "en-US",
  "displayName": "long audio synthesis sample",
  "description": "sample description"
}
```

A partir da `status` propriedade, pode ler o estado deste pedido. O pedido começará a partir do `NotStarted` estado, em seguida, mudar para `Running` , e finalmente tornar-se `Succeeded` ou `Failed` . Pode utilizar um loop para sondar esta API até que o estado se torne `Succeeded` .

### <a name="download-audio-result"></a>Baixar resultado sonoro

Uma vez que um pedido de síntese tenha sucesso, você pode baixar o resultado áudio ligando para `/files` GET API.

```python
def get_files():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/files'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_files()
```
`<request_id>`Substitua-o pelo ID de pedido que pretende fazer o download do resultado. Pode ser encontrado na resposta do passo anterior.

A saída será assim:
```console
response.status_code: 200
{
  "values": [
    {
      "name": "2779f2aa-4e21-4d13-8afb-6b3104d6661a.txt",
      "kind": "LongAudioSynthesisScript",
      "properties": {
        "size": 4200
      },
      "createdDateTime": "2021-01-14T11:11:02.410Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/input.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "name": "voicesynthesis_waves.zip",
      "kind": "LongAudioSynthesisResult",
      "properties": {
        "size": 9290000
      },
      "createdDateTime": "2021-01-14T11:12:27.226Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/voicesynthesis_waves.zip?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ]
}
```
A saída contém informações de 2 ficheiros. O que `"kind": "LongAudioSynthesisScript"` tem é o script de entrada submetido. O outro com `"kind": "LongAudioSynthesisResult"` é o resultado deste pedido.
O resultado é zip que contém os ficheiros de saída de áudio gerados, juntamente com uma cópia do texto de entrada.

Ambos os ficheiros podem ser descarregados a partir do URL na sua `links.contentUrl` propriedade.

### <a name="get-all-synthesis-requests"></a>Receba todos os pedidos de síntese

Pode obter uma lista de todos os pedidos submetidos com o seguinte código:

```python
def get_synthesis():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/'.format(region)    
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_synthesis()
```

A saída será como:
```console
response.status_code: 200
{
  "values": [
    {
      "models": [
        {
          "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141",
          "voiceName": "my custom neural voice"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT1S",
        "billableCharacterCount": 5
      },
      "id": "f9f0bb74-dfa5-423d-95e7-58a5e1479315",
      "lastActionDateTime": "2021-01-05T07:25:42.433Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-05T07:25:13.600Z",
      "locale": "en-US",
      "displayName": "Long Audio Synthesis",
      "description": "Long audio synthesis sample"
    },
    {
      "models": [
        {
          "voiceName": "en-US-AriaNeural"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT5M57.252S",
        "billableCharacterCount": 3048
      },
      "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
      "lastActionDateTime": "2021-01-14T11:12:27.240Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-14T11:11:02.557Z",
      "locale": "en-US",
      "displayName": "long audio synthesis sample",
      "description": "sample description"
    }
  ]
}
```

`values` propriedade contém uma lista de pedidos de síntese. A lista é paginada, com um tamanho máximo de página de 100. Se houver mais de 100 pedidos, será fornecido um `"@nextLink"` imóvel para obter a próxima página da lista paginada.

```console
  "@nextLink": "https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/?top=100&skip=100"
```

Também pode personalizar o tamanho da página e o número de salto, fornecendo `skip` e `top` no parâmetro URL.

### <a name="remove-previous-requests"></a>Remover pedidos anteriores

O serviço irá manter até **20.000** pedidos para cada conta de subscrição da Azure. Se o valor do seu pedido exceder esta limitação, por favor remova os pedidos anteriores antes de esvoaçar os novos. Se não remover os pedidos existentes, receberá uma notificação de erro.

O código que se segue mostra como remover um pedido específico de síntese.
```python
def delete_synthesis():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.delete(url, headers=header)
    print('response.status_code: %d' % response.status_code)
```

Se o pedido for removido com sucesso, o código de estado de resposta será HTTP 204 (Sem Conteúdo).

```console
response.status_code: 204
```

> [!NOTE]
> Pedidos com estatuto de `NotStarted` ou não podem ser `Running` removidos ou eliminados.

O concluído `long_audio_synthesis_client.py` está disponível no [GitHub.](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py)

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

| Region | Ponto final |
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
