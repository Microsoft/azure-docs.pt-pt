---
title: Converter texto-a-fala, Python - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprenderá a converter texto-a-fala usando Python e a API de DESCANSO Texto-a-Fala. O texto da amostra incluído neste guia é estruturado como Linguagem de Marcação da Síntese da Fala (SSML). Isto permite-lhe escolher a voz e a linguagem da resposta da fala.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 171fdb033cba422d8ba580da3ab54db88ca20872
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81400821"
---
# <a name="convert-text-to-speech-using-python"></a>Converter texto-a-fala usando Python

Neste artigo, você aprenderá a converter texto-a-fala usando Python e a API REST text-to-speech. O corpo de pedido neste guia é estruturado como Linguagem de Marcação de Síntese de [Fala (SSML),](speech-synthesis-markup.md)que lhe permite escolher a voz e a linguagem da resposta.

Este artigo requer uma [conta de Serviços Cognitivos Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de serviço da Fala. Se não tiver uma conta, pode utilizar a [avaliação gratuita](get-started.md) para obter uma chave de subscrição.

## <a name="prerequisites"></a>Pré-requisitos

* Python 2.7.x ou 3.x
* Estúdio Visual , <a href="https://code.visualstudio.com/download" target="_blank">Código <span class="docon docon-navigate-external x-hidden-focus"> </span>de Estúdio Visual, </a>ou o seu editor de texto favorito <a href="https://visualstudio.microsoft.com/downloads/" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>
* Uma chave de subscrição Azure para o serviço De Fala

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

Estes módulos são usados para escrever a resposta da fala a um ficheiro com um carimbo de tempo, construir o pedido HTTP, e chamar a API texto-a-fala.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Desloque a chave de subscrição e crie um pedido para o TTS

Nas próximas secções, irá criar métodos para lidar com a autorização, chamar a API de texto a falar e validar a resposta. Vamos começar por adicionar um código que garante que esta amostra funcionará com Python 2.7.x e 3.x.

```python
try:
    input = raw_input
except NameError:
    pass
```

Em seguida, vamos criar uma classe. É aqui que vamos colocar os nossos métodos de troca simbólica, e chamar a API de texto para discurso.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

É `subscription_key` a sua chave única do portal Azure. `tts`solicita ao utilizador que introduza texto que será convertido em fala. Esta entrada é uma corda literal, por isso os personagens não precisam de ser escapados. Finalmente, `timestr` obtém o tempo atual, que usaremos para nomear o seu ficheiro.

## <a name="get-an-access-token"></a>Obter um token de acesso

A API REST text-to-speech requer um sinal de acesso para autenticação. Para obter um sinal de acesso, é necessária uma troca. Esta amostra troca a sua chave de subscrição `issueToken` do serviço Speech por um token de acesso utilizando o ponto final.

Esta amostra pressupõe que a sua subscrição de serviço speech está na região dos EUA Ocidentais. Se estiver a utilizar uma região `fetch_token_url`diferente, atualize o valor para . Para obter uma lista completa, consulte [Regiões.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)

Copie este `TextToSpeech` código para a classe:

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
> Para obter mais informações sobre a autenticação, consulte [Authenticate com um sinal de acesso](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Faça um pedido e poupe a resposta

Aqui vai supor o pedido e salvar a resposta do discurso. Primeiro, tens de `base_url` definir `path`o e. Esta amostra pressupõe que estás a usar o ponto final dos EUA Ocidentais. Se o seu recurso estiver registado numa região diferente, certifique-se de que atualiza o `base_url`. Para mais informações, consulte [as regiões de serviço da Fala.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)

Em seguida, você precisa adicionar cabeçalhos necessários para o pedido. Certifique-se de `User-Agent` que atualiza com o nome do seu recurso `X-Microsoft-OutputFormat` (localizado no portal Azure) e que se desloque para a sua saída de áudio preferida. Para obter uma lista completa dos formatos de saída, consulte [as saídas de áudio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Em seguida, construa o corpo de pedido usando a linguagem de marcação da síntese da fala (SSML). Esta amostra define a estrutura `tts` e utiliza a entrada que criou anteriormente.

>[!NOTE]
> Esta amostra `Guy24kRUS` utiliza a fonte de voz. Para obter uma lista completa da Microsoft forneceu vozes/idiomas, consulte [o suporte do Idioma](language-support.md).
> Se está interessado em criar uma voz única e reconhecível para a sua marca, consulte Criar fontes de [voz personalizadas.](how-to-customize-voice-font.md)

Finalmente, fará um pedido ao serviço. Se o pedido for bem sucedido, e um código de estado de 200 for devolvido, a resposta da fala é escrita para um ficheiro timestamped.

Copie este `TextToSpeech` código para a classe:

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

Está quase concluído. O último passo é instantaneamente a sua aula e chamar as suas funções.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Está pronto para executar a sua aplicação de amostra sms-to-speech. A partir da linha de comando (ou sessão terminal), navegue até ao seu diretório de projeto e corra:

```console
python tts.py
```

Quando for solicitado, escreva o que quiser converter de texto para discurso. Se for bem sucedido, o ficheiro de fala está localizado na pasta do projeto. Jogue usando o seu leitor de mídia favorito.

## <a name="clean-up-resources"></a>Limpar recursos

Certifique-se de remover quaisquer informações confidenciais do código fonte da sua aplicação de amostra, como as chaves de subscrição.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore as amostras python no GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Consulte também

* [Referência da API de conversão de texto em voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Usando Python e Speech SDK para converter texto-a-fala](quickstarts/speech-to-text-from-microphone.md)
* [Criação de fontes de voz personalizadas](how-to-customize-voice-font.md)
* [Gravar amostras de voz para criar uma voz personalizada](record-custom-voice-samples.md)
