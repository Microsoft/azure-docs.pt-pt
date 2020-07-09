---
title: Converter texto-a-discurso, Python - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprenderá a converter texto-a-discurso usando Python e a API text-to-speech REST. O texto da amostra incluído neste guia é estruturado como Linguagem de Marcação de Síntese de Fala (SSML). Isto permite-lhe escolher a voz e a linguagem da resposta da fala.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 04/13/2020
ms.author: trbye
ms.custom: tracking-python
ms.openlocfilehash: 525417bd83a1d30479fd3effbce690ed04d9af73
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84608010"
---
# <a name="convert-text-to-speech-using-python"></a>Converter texto-a-discurso usando Python

Neste artigo, você aprenderá a converter texto-a-discurso usando Python e a API DE REPOUSO text-to-speech. O órgão de pedido neste guia é estruturado como Linguagem de Marcação de [Síntese de Fala (SSML),](speech-synthesis-markup.md)que lhe permite escolher a voz e a linguagem da resposta.

Este artigo requer uma [conta Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de serviço de fala. Se não tiver uma conta, pode utilizar a [avaliação gratuita](get-started.md) para obter uma chave de subscrição.

## <a name="prerequisites"></a>Pré-requisitos

* Python 2.7.x ou 3.x
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual <span class="docon docon-navigate-external x-hidden-focus"></span> Studio </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code, <span class="docon docon-navigate-external x-hidden-focus"></span> </a>ou o seu editor de texto favorito
* Uma chave de subscrição Azure para o serviço Speech

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos exigidos

Crie um novo projeto do Python através do seu editor ou IDE favorito. Em seguida, copie este fragmento de código para o seu projeto num ficheiro com o nome `tts.py`.

```python
import os
import requests
import time
from xml.etree import ElementTree
```

> [!NOTE]
> Se ainda não utilizou estes módulos, terá de instalá-los antes de executar o seu programa. Para instalar estes pacotes, execute: `pip install requests`.

Estes módulos são utilizados para escrever a resposta da fala a um ficheiro com um timetamp, construir o pedido HTTP e chamar a API de texto-a-fala.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Desaça a chave de subscrição e crie um pedido para TTS

Nas próximas secções irá criar métodos para lidar com a autorização, ligar para a API de texto-a-voz e validar a resposta. Vamos começar por adicionar algum código que garanta que esta amostra funcione com Python 2.7.x e 3.x.

```python
try:
    input = raw_input
except NameError:
    pass
```

A seguir, vamos criar uma aula. É aqui que vamos colocar os nossos métodos para troca de fichas, e chamar a API de texto-a-discurso.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

É `subscription_key` a sua chave única do portal Azure. `tts`solicita ao utilizador que introduza texto que será convertido para discurso. Esta entrada é uma corda literal, por isso os personagens não precisam de ser escapados. Finalmente, `timestr` obtém a hora atual, que usaremos para dar o nome ao seu ficheiro.

## <a name="get-an-access-token"></a>Obter um token de acesso

A API DE REPOUSO text-to-speech requer um sinal de acesso para autenticação. Para obter um token de acesso, é necessária uma troca. Esta amostra troca a sua chave de subscrição do serviço Speech para um token de acesso utilizando o `issueToken` ponto final.

Esta amostra pressupõe que a sua subscrição de serviço de Discurso está na região oeste dos EUA. Se estiver a utilizar uma região diferente, atualize o valor para `fetch_token_url` . Para obter uma lista completa, consulte [regiões.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)

Copie este código para a `TextToSpeech` classe:

```python
def get_token(self):
    fetch_token_url = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    headers = {
        'Ocp-Apim-Subscription-Key': self.subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    self.access_token = str(response.text)
```

> [!NOTE]
> Para obter mais informações sobre a autenticação, consulte [Authenticate com um token de acesso.](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token)

## <a name="make-a-request-and-save-the-response"></a>Faça um pedido e guarde a resposta

Aqui vais construir o pedido e salvar a resposta da fala. Primeiro, tens de definir o `base_url` `path` e. Esta amostra assume que estás a usar o ponto final dos EUA. Se o seu recurso estiver registado numa região diferente, certifique-se de que atualiza o `base_url` . Para mais informações, consulte [as regiões de serviços de fala.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)

Em seguida, tem de adicionar os cabeçalhos necessários para o pedido. Certifique-se de que atualiza `User-Agent` com o nome do seu recurso (localizado no portal Azure) e definido para a sua saída de áudio `X-Microsoft-OutputFormat` preferida. Para obter uma lista completa dos formatos de saída, consulte [as saídas áudio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Em seguida, construa o corpo de pedido usando a linguagem de marcação da síntese da fala (SSML). Esta amostra define a estrutura e utiliza a `tts` entrada que criou anteriormente.

>[!NOTE]
> Esta amostra utiliza o `Guy24kRUS` tipo de letra de voz. Para obter uma lista completa de vozes/idiomas fornecidos pela Microsoft, consulte [o suporte linguístico](language-support.md).
> Se estiver interessado em criar uma voz única e reconhecível para a sua marca, consulte [criar fontes de voz personalizadas.](how-to-customize-voice-font.md)

Finalmente, fará um pedido ao serviço. Se o pedido for bem sucedido e um código de estado de 200 for devolvido, a resposta da fala é escrita para um ficheiro com tempos.

Copie este código para a `TextToSpeech` classe:

```python
def save_audio(self):
    base_url = 'https://westus.tts.speech.microsoft.com/'
    path = 'cognitiveservices/v1'
    constructed_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token,
        'Content-Type': 'application/ssml+xml',
        'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
        'User-Agent': 'YOUR_RESOURCE_NAME'
    }
    xml_body = ElementTree.Element('speak', version='1.0')
    xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
    voice = ElementTree.SubElement(xml_body, 'voice')
    voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
    voice.set(
        'name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)')
    voice.text = self.tts
    body = ElementTree.tostring(xml_body)

    response = requests.post(constructed_url, headers=headers, data=body)
    if response.status_code == 200:
        with open('sample-' + self.timestr + '.wav', 'wb') as audio:
            audio.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nYour TTS is ready for playback.\n")
    else:
        print("\nStatus code: " + str(response.status_code) +
              "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Juntar tudo

Está quase concluído. O último passo é instantanear a sua aula e ligar para as suas funções.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

É isso, estás pronto para executar a tua aplicação de amostra de texto-a-fala. A partir da linha de comando (ou sessão terminal), navegue até ao diretório do projeto e corra:

```console
python tts.py
```

Quando solicitado, digite o que quiser converter de texto para discurso. Se for bem sucedido, o ficheiro de fala está localizado na sua pasta de projeto. Jogue-o usando o seu leitor de media favorito.

## <a name="clean-up-resources"></a>Limpar recursos

Certifique-se de remover qualquer informação confidencial do código fonte da sua aplicação de amostra, como chaves de subscrição.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Explore as amostras de Python no GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Consulte também

* [Referência da API de conversão de texto em voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Usando Python e Speech SDK para converter texto-a-discurso](quickstarts/speech-to-text-from-microphone.md)
* [Criando fontes de voz personalizadas](how-to-customize-voice-font.md)
* [Registar amostras de voz para criar uma voz personalizada](record-custom-voice-samples.md)
