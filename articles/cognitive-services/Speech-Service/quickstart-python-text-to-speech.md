---
title: Converter texto-a-fala, Python - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprenderá a converter texto-a-fala usando Python e a API de DESCANSO Texto-a-Fala. O texto de exemplo incluído neste guia está estruturado como Speech Synthesis Markup Language (SSML). Isto permite-lhe escolher a voz e o idioma da resposta de voz.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: 63e6a2a47265eae08a653f3eadaf6bad86dd0635
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119724"
---
# <a name="convert-text-to-speech-using-python"></a>Converter texto-a-fala usando Python

Neste artigo, você aprenderá a converter texto-a-fala usando Python e a API REST text-to-speech. O corpo de pedido neste guia é estruturado como Linguagem de Marcação de Síntese de [Fala (SSML),](speech-synthesis-markup.md)que lhe permite escolher a voz e a linguagem da resposta.

Este artigo requer uma [conta de Serviços Cognitivos Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de serviço da Fala. Se não tiver uma conta, pode utilizar a [avaliação gratuita](get-started.md) para obter uma chave de subscrição.

## <a name="prerequisites"></a>Pré-requisitos

* Python 2.7.x ou 3.x
* [Estúdio Visual](https://visualstudio.microsoft.com/downloads/), [Código de Estúdio Visual,](https://code.visualstudio.com/download)ou o seu editor de texto favorito
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

Estes módulos são utilizados para escrever a resposta de voz para um ficheiro com um carimbo, construa a solicitação HTTP e chamar a API de texto para voz.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Defina a chave de subscrição e criar uma linha de comandos para TTS

As próximas seções irá criar métodos para processar autorizações, chamar a API de texto para voz e validar a resposta. Vamos começar por adicionar um código que certifica-se de que este exemplo irá funcionar com o Python 2.7.x e 3.x.

```python
try:
    input = raw_input
except NameError:
    pass
```

Em seguida, vamos criar uma classe. Isso é onde, colocamos nossa métodos para troca de token e chamar a API de texto para voz.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

O `subscription_key` é a sua chave única do portal Azure. `tts` leva o utilizador a introduzir texto que será convertido para a fala. Esta entrada é uma cadeia literal, para que os caracteres não precisam de caracteres de escape. Finalmente, `timestr` tem o tempo atual, que usaremos para nomear o seu ficheiro.

## <a name="get-an-access-token"></a>Obter um token de acesso

A API REST do texto para discurso exige um token de acesso para a autenticação. Para obter um token de acesso, um exchange é necessário. Esta amostra troca a sua chave de subscrição do serviço Speech por um token de acesso utilizando o ponto final `issueToken`.

Esta amostra pressupõe que a sua subscrição de serviço speech está na região dos EUA Ocidentais. Se estiver a utilizar uma região diferente, atualize o valor para `fetch_token_url`. Para obter uma lista completa, consulte [Regiões.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)

Copie este código para a classe `TextToSpeech`:

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

## <a name="make-a-request-and-save-the-response"></a>Fazer um pedido e guardar a resposta

Aqui pretende criar o pedido e guardar a resposta de voz. Primeiro, tens de definir o `base_url` e `path`. Este exemplo parte do princípio de que está a utilizar o ponto de extremidade do E.U.A. oeste. Se o seu recurso estiver registado numa região diferente, certifique-se de atualizar o `base_url`. Para mais informações, consulte [as regiões de serviço da Fala.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)

Em seguida, terá de adicionar cabeçalhos necessários para o pedido. Certifique-se de que atualiza `User-Agent` com o nome do seu recurso (localizado no portal Azure), e `X-Microsoft-OutputFormat` para a sua saída de áudio preferida. Para obter uma lista completa dos formatos de saída, consulte [as saídas de áudio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Em seguida, construa o corpo do pedido com o Speech Synthesis Markup Language (SSML). Esta amostra define a estrutura e utiliza a entrada `tts` que criou anteriormente.

>[!NOTE]
> Esta amostra utiliza a fonte de voz `Guy24KRUS`. Para obter uma lista completa da Microsoft forneceu vozes/idiomas, consulte [o suporte do Idioma](language-support.md).
> Se está interessado em criar uma voz única e reconhecível para a sua marca, consulte Criar fontes de [voz personalizadas.](how-to-customize-voice-font.md)

Por fim, vou fazer um pedido para o serviço. Se a solicitação for bem-sucedida, e é devolvido um código de 200 Estado, a resposta de voz é escrita num ficheiro timestamped.

Copie este código para a classe `TextToSpeech`:

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
        'name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
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

Está quase concluído. A última etapa é instanciar sua classe e chamar as suas funções.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

É isso, está pronto para executar a aplicação de exemplo de texto para voz. A partir da linha de comandos (ou sessão de terminal), navegue para o diretório de projeto e execute:

```console
python tts.py
```

Quando lhe for pedido, escreva qualquer coisa que gostaria de converter a síntese de voz. Se tiver êxito, o ficheiro de voz está localizado na pasta do projeto. Reproduzi-lo usando o leitor de multimédia favorito.

## <a name="clean-up-resources"></a>Limpar recursos

Não se esqueça de remover quaisquer informações confidenciais do código-fonte da sua aplicação de exemplo, como chaves de subscrição.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore as amostras python no GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Veja também

* [Referência da API de conversão de texto em voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Usando Python e Speech SDK para converter texto-a-fala](quickstarts/speech-to-text-from-microphone.md)
* [Criação de fontes de voz personalizadas](how-to-customize-voice-font.md)
* [Gravar amostras de voz para criar uma voz personalizada](record-custom-voice-samples.md)
