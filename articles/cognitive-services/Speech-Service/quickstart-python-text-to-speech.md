---
title: 'Início rápido: Converter a voz, Python - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, irá aprender como converter texto para voz com Python e a API de REST de voz. O texto de exemplo incluído neste guia está estruturado como Speech Synthesis Markup Language (SSML). Isto permite-lhe escolher a voz e o idioma da resposta de voz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 087440b60e1d5fecc668849bc1350d66988b16b9
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339094"
---
# <a name="quickstart-convert-text-to-speech-using-python"></a>Início rápido: Converter texto para voz com Python

Neste início rápido, irá aprender como converter texto para voz com Python e a API REST do texto para voz. O corpo do pedido neste guia está estruturado como [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md), que permite-lhe escolher a voz e o idioma da resposta.

Este início rápido requer uma [conta dos serviços cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de serviços de voz. Se não tiver uma conta, pode utilizar a [avaliação gratuita](get-started.md) para obter uma chave de subscrição.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Python 2.7.x ou 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), ou no seu editor de texto favorito
* Uma chave de subscrição do Azure para os serviços de voz

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos exigidos

Crie um novo projeto do Python através do seu editor ou IDE favorito. Em seguida, copie este fragmento de código para o seu projeto num ficheiro com o nome `tts.py`.

```python
import os, requests, time
from xml.etree import ElementTree
```

> [!NOTE]
> Se ainda não utilizou estes módulos, terá de instalá-los antes de executar o seu programa. Para instalar estes pacotes, execute: `pip install requests`.

Estes módulos são utilizados para escrever a resposta de voz para um ficheiro com um carimbo, construa a solicitação HTTP e chamar a API de texto para voz.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Defina a chave de subscrição e criar uma linha de comandos para TTS

As próximas seções irá criar métodos para processar autorizações, chamar a API de texto para voz e validar a resposta. Vamos começar por adicionar um código que certifica-se de que este exemplo irá funcionar com o Python 2.7.x e 3.x.

```python
try: input = raw_input
except NameError: pass
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

O `subscription_key` é a chave exclusiva do portal do Azure. `tts` pede ao utilizador para introduzir o texto que será convertido para voz. Esta entrada é uma cadeia literal, para que os caracteres não precisam de caracteres de escape. Por fim, `timestr` obtém a hora atual, o que usaremos para nomear o arquivo.

## <a name="get-an-access-token"></a>Obter um token de acesso

A API REST do texto para discurso exige um token de acesso para a autenticação. Para obter um token de acesso, um exchange é necessário. Este exemplo troca sua chave de subscrição de serviços de voz para um token de acesso através do `issueToken` ponto final.

Este exemplo parte do princípio de que a sua subscrição de serviços de voz está na região E.U.A. oeste. Se estiver a utilizar uma região diferente, atualize o valor para `fetch_token_url`. Para obter uma lista completa, consulte [regiões](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Copie este código para o `TextToSpeech` classe:

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

## <a name="make-a-request-and-save-the-response"></a>Fazer um pedido e guardar a resposta

Aqui pretende criar o pedido e guardar a resposta de voz. Em primeiro lugar, tem de definir o `base_url` e `path`. Este exemplo parte do princípio de que está a utilizar o ponto de extremidade do E.U.A. oeste. Se o recurso está registado para uma região diferente, certifique-se de que atualiza o `base_url`. Para obter mais informações, consulte [regiões de serviços de voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Em seguida, terá de adicionar cabeçalhos necessários para o pedido. Certifique-se de que Atualize `User-Agent` com o nome do recurso (localizado no portal do Azure) e conjunto `X-Microsoft-OutputFormat` para a saída de áudio preferencial. Para obter uma lista completa dos formatos de saída, consulte [saídas de áudio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Em seguida, construa o corpo do pedido com o Speech Synthesis Markup Language (SSML). Este exemplo define a estrutura e utiliza o `tts` de entrada que criou anteriormente.

>[!NOTE]
> Este exemplo utiliza o `ZiraRUS` tipo de voz. Para uma lista completa de Microsoft fornecidas vozes/idiomas, consulte [suporte de idiomas](language-support.md).
> Se estiver interessado em criar uma voz exclusiva, reconhecível para sua marca, veja [criar tipos de voz personalizada](how-to-customize-voice-font.md).

Por fim, vou fazer um pedido para o serviço. Se a solicitação for bem-sucedida, e é devolvido um código de 200 Estado, a resposta de voz é escrita num ficheiro timestamped.

Copie este código para o `TextToSpeech` classe:

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
    voice.set('name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
    voice.text = self.tts
    body = ElementTree.tostring(xml_body)

    response = requests.post(constructed_url, headers=headers, data=body)
    if response.status_code == 200:
        with open('sample-' + self.timestr + '.wav', 'wb') as audio:
            audio.write(response.content)
            print("\nStatus code: " + str(response.status_code) + "\nYour TTS is ready for playback.\n")
    else:
        print("\nStatus code: " + str(response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")
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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explorar amostras de Python no GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Consulte também

* [Referência da API de conversão de texto em voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Criar tipos de voz personalizada](how-to-customize-voice-font.md)
* [Exemplos de voz de registo para criar uma voz personalizada](record-custom-voice-samples.md)
