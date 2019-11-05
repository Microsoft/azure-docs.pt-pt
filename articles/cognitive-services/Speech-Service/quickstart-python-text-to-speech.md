---
title: 'Início rápido: converter conversão de texto em fala, serviço de fala em Python'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você aprenderá a converter conversão de texto em fala usando o Python e a API REST de conversão de texto em fala. O texto de exemplo incluído neste guia é estruturado como uma linguagem de marcação de síntese de fala (SSML). Isso permite que você escolha a voz e o idioma da resposta de fala.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: a66835d605b9005b8f94eb79a3c266f735f0a3b6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467210"
---
# <a name="quickstart-convert-text-to-speech-using-python"></a>Início rápido: converter conversão de texto em fala usando Python

Neste guia de início rápido, você aprenderá a converter conversão de texto em fala usando o Python e a API REST de conversão de texto em fala. O corpo da solicitação neste guia é estruturado como uma [linguagem de marcação de síntese de fala (SSML)](speech-synthesis-markup.md), que permite escolher a voz e o idioma da resposta.

Este início rápido requer uma [conta de serviços cognitivas do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de serviços de fala. Se não tiver uma conta, pode utilizar a [avaliação gratuita](get-started.md) para obter uma chave de subscrição.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Python 2.7.x ou 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download)ou seu editor de texto favorito
* Uma chave de assinatura do Azure para os serviços de fala

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

Esses módulos são usados para gravar a resposta de fala em um arquivo com um carimbo de data/hora, construir a solicitação HTTP e chamar a API de conversão de texto em fala.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Definir a chave de assinatura e criar um prompt para TTS

Nas próximas seções, você criará métodos para lidar com a autorização, chamará a API de conversão de texto em fala e validará a resposta. Vamos começar adicionando algum código que verifique se esse exemplo funcionará com Python 2.7. x e 3. x.

```python
try:
    input = raw_input
except NameError:
    pass
```

Em seguida, vamos criar uma classe. É aí que vamos colocar nossos métodos para troca de token e chamar a API de conversão de texto em fala.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

O `subscription_key` é sua chave exclusiva do portal do Azure. `tts` solicita que o usuário insira o texto que será convertido em fala. Essa entrada é um literal de cadeia de caracteres, portanto, os caracteres não precisam ser ignorados. Por fim, `timestr` Obtém a hora atual, que usaremos para nomear o arquivo.

## <a name="get-an-access-token"></a>Obter um token de acesso

A API REST de conversão de texto em fala requer um token de acesso para autenticação. Para obter um token de acesso, é necessária uma troca. Este exemplo troca sua chave de assinatura dos serviços de fala para um token de acesso usando o ponto de extremidade `issueToken`.

Este exemplo pressupõe que sua assinatura de serviços de fala está na região oeste dos EUA. Se você estiver usando uma região diferente, atualize o valor para `fetch_token_url`. Para obter uma lista completa, consulte [regiões](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

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
> Para obter mais informações sobre autenticação, consulte [autenticar com um token de acesso](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Fazer uma solicitação e salvar a resposta

Aqui, você vai criar a solicitação e salvar a resposta de fala. Primeiro, você precisa definir o `base_url` e `path`. Este exemplo pressupõe que você esteja usando o ponto de extremidade oeste dos EUA. Se o recurso estiver registrado em uma região diferente, certifique-se de atualizar o `base_url`. Para obter mais informações, consulte [regiões de serviços de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Em seguida, você precisa adicionar os cabeçalhos necessários para a solicitação. Certifique-se de atualizar `User-Agent` com o nome do seu recurso (localizado na portal do Azure) e defina `X-Microsoft-OutputFormat` para sua saída de áudio preferencial. Para obter uma lista completa dos formatos de saída, consulte [saídas de áudio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Em seguida, construa o corpo da solicitação usando a linguagem de marcação de síntese de fala (SSML). Este exemplo define a estrutura e usa a entrada `tts` que você criou anteriormente.

>[!NOTE]
> Este exemplo usa a fonte de voz `Guy24KRUS`. Para obter uma lista completa de vozes/idiomas fornecidos pela Microsoft, consulte [suporte a idiomas](language-support.md).
> Se você estiver interessado em criar uma voz exclusiva e reconhecível para sua marca, consulte [criando fontes de voz personalizadas](how-to-customize-voice-font.md).

Por fim, você fará uma solicitação para o serviço. Se a solicitação for bem-sucedida e um código de status 200 for retornado, a resposta de fala será gravada em um arquivo com carimbo de data/hora.

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

Está quase concluído. A última etapa é instanciar sua classe e chamar suas funções.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

É isso, você está pronto para executar o aplicativo de exemplo de conversão de texto em fala. Na linha de comando (ou sessão de terminal), navegue até o diretório do projeto e execute:

```console
python tts.py
```

Quando solicitado, digite o que você deseja converter de conversão de texto em fala. Se for bem-sucedido, o arquivo de fala estará localizado na pasta do projeto. Jogue usando seu player de mídia favorito.

## <a name="clean-up-resources"></a>Limpar recursos

Certifique-se de remover todas as informações confidenciais do código-fonte do aplicativo de exemplo, como chaves de assinatura.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore os exemplos do Python no GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Consultar também

* [Referência da API de conversão de texto em voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Usando o Python e o SDK de fala para converter conversão de texto em fala](quickstarts/speech-to-text-from-microphone.md)
* [Criando fontes de voz personalizadas](how-to-customize-voice-font.md)
* [Gravar amostras de voz para criar uma voz personalizada](record-custom-voice-samples.md)
