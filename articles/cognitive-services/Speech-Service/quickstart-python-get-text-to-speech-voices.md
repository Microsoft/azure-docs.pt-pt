---
title: Lista vozes texto-a-discurso, Python - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprenderá a obter a lista completa de vozes padrão e neurais para uma região/ponto final usando Python. A lista é devolvida como JSON, e a disponibilidade de voz varia por região.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 04/13/2020
ms.author: trbye
ms.custom: tracking-python
ms.openlocfilehash: ec1b03bf0b3cf95f65013bddbc54e15ab985198e
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84608007"
---
# <a name="get-the-list-of-text-to-speech-voices-using-python"></a>Obtenha a lista de vozes de texto-a-voz usando Python

Neste artigo, você aprenderá a obter a lista completa de vozes padrão e neurais para uma região/ponto final usando Python. A lista é devolvida como JSON, e a disponibilidade de voz varia por região. Para obter uma lista de regiões apoiadas, consulte [as regiões.](regions.md)

Este artigo requer uma [conta Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de serviço de fala. Se não tiver uma conta, pode utilizar a [avaliação gratuita](get-started.md) para obter uma chave de subscrição.

## <a name="prerequisites"></a>Pré-requisitos

* Python 2.7.x ou 3.x
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual <span class="docon docon-navigate-external x-hidden-focus"></span> Studio </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code, <span class="docon docon-navigate-external x-hidden-focus"></span> </a>ou o seu editor de texto favorito
* Uma chave de subscrição Azure para o serviço Speech

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos exigidos

Crie um novo projeto do Python através do seu editor ou IDE favorito. Em seguida, copie este fragmento de código para o seu projeto num ficheiro com o nome `get-voices.py`.

```python
import requests
```

> [!NOTE]
> Se ainda não utilizou estes módulos, terá de instalá-los antes de executar o seu programa. Para instalar estes pacotes, execute: `pip install requests`.

Os pedidos são utilizados para pedidos HTTP ao serviço de texto-a-voz.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Desaça a chave de subscrição e crie um pedido para TTS

Nas próximas secções irá criar métodos para lidar com a autorização, ligar para a API de texto-a-voz e validar a resposta. Vamos começar por criar uma aula. É aqui que vamos colocar os nossos métodos para troca de fichas, e chamar a API de texto-a-discurso.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

É `subscription_key` a sua chave única do portal Azure.

## <a name="get-an-access-token"></a>Obter um token de acesso

Este ponto final requer um sinal de acesso para autenticação. Para obter um token de acesso, é necessária uma troca. Esta amostra troca a sua chave de subscrição do serviço Speech para um token de acesso utilizando o `issueToken` ponto final.

Esta amostra pressupõe que a sua subscrição de serviço de Discurso está na região oeste dos EUA. Se estiver a utilizar uma região diferente, atualize o valor para `fetch_token_url` . Para obter uma lista completa, consulte [regiões.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)

Copie este código para a `GetVoices` classe:

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

Aqui vais construir o pedido e guardar a lista de vozes devolvidas. Primeiro, tens de definir o `base_url` `path` e. Esta amostra assume que estás a usar o ponto final dos EUA. Se o seu recurso estiver registado numa região diferente, certifique-se de que atualiza o `base_url` . Para mais informações, consulte [as regiões de serviços de fala.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)

Em seguida, adicione os cabeçalhos necessários para o pedido. Finalmente, fará um pedido ao serviço. Se o pedido for bem sucedido e um código de estado de 200 for devolvido, a resposta será escrita para arquivar.

Copie este código para a `GetVoices` classe:

```python
def get_voices(self):
    base_url = 'https://westus.tts.speech.microsoft.com'
    path = '/cognitiveservices/voices/list'
    get_voices_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token
    }
    response = requests.get(get_voices_url, headers=headers)
    if response.status_code == 200:
        with open('voices.json', 'wb') as voices:
            voices.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nvoices.json is ready to view.\n")
    else:
        print("\nStatus code: " + str(
            response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Juntar tudo

Está quase concluído. O último passo é instantanear a sua aula e ligar para as suas funções.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = GetVoices(subscription_key)
    app.get_token()
    app.get_voices()
```

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Está pronto para analisar a amostra. A partir da linha de comando (ou sessão terminal), navegue até ao diretório do projeto e corra:

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Limpar recursos

Certifique-se de remover qualquer informação confidencial do código fonte da sua aplicação de amostra, como chaves de subscrição.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Explore as amostras de Python no GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Consulte também

* [Referência da API de conversão de texto em voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Criando fontes de voz personalizadas](how-to-customize-voice-font.md)
* [Registar amostras de voz para criar uma voz personalizada](record-custom-voice-samples.md)
