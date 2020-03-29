---
title: Lista de vozes de texto para discurso, Python - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprenderá como obter a lista completa de vozes padrão e neurais para uma região/ponto final usando Python. A lista é devolvida como JSON, e a disponibilidade de voz varia por região.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: 51fe6cea80e097f34432ab8dc7293c758bd8d720
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77119803"
---
# <a name="get-the-list-of-text-to-speech-voices-using-python"></a>Obtenha a lista de vozes de texto-a-fala usando Python

Neste artigo, você aprenderá como obter a lista completa de vozes padrão e neurais para uma região/ponto final usando Python. A lista é devolvida como JSON, e a disponibilidade de voz varia por região. Para obter uma lista de regiões apoiadas, consulte [regiões.](regions.md)

Este artigo requer uma [conta de Serviços Cognitivos Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de serviço da Fala. Se não tiver uma conta, pode utilizar a [avaliação gratuita](get-started.md) para obter uma chave de subscrição.

## <a name="prerequisites"></a>Pré-requisitos

* Python 2.7.x ou 3.x
* [Estúdio Visual](https://visualstudio.microsoft.com/downloads/), [Código de Estúdio Visual,](https://code.visualstudio.com/download)ou o seu editor de texto favorito
* Uma chave de subscrição Azure para o serviço De Fala

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos exigidos

Crie um novo projeto do Python através do seu editor ou IDE favorito. Em seguida, copie este fragmento de código para o seu projeto num ficheiro com o nome `get-voices.py`.

```python
import requests
```

> [!NOTE]
> Se ainda não utilizou estes módulos, terá de instalá-los antes de executar o seu programa. Para instalar estes pacotes, execute: `pip install requests`.

Os pedidos são utilizados para pedidos http ao serviço de texto-a-fala.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Desloque a chave de subscrição e crie um pedido para o TTS

Nas próximas secções, irá criar métodos para lidar com a autorização, chamar a API de texto a falar e validar a resposta. Vamos começar por criar uma aula. É aqui que vamos colocar os nossos métodos de troca simbólica, e chamar a API de texto para discurso.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

É `subscription_key` a sua chave única do portal Azure.

## <a name="get-an-access-token"></a>Obter um token de acesso

Este ponto final requer um sinal de acesso para autenticação. Para obter um sinal de acesso, é necessária uma troca. Esta amostra troca a sua chave de subscrição `issueToken` do serviço Speech por um token de acesso utilizando o ponto final.

Esta amostra pressupõe que a sua subscrição de serviço speech está na região dos EUA Ocidentais. Se estiver a utilizar uma região `fetch_token_url`diferente, atualize o valor para . Para obter uma lista completa, consulte [Regiões.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)

Copie este `GetVoices` código para a classe:

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

Aqui vais construir o pedido e salvar a lista de vozes devolvidas. Primeiro, tens de `base_url` definir `path`o e. Esta amostra pressupõe que estás a usar o ponto final dos EUA Ocidentais. Se o seu recurso estiver registado numa região diferente, certifique-se de que atualiza o `base_url`. Para mais informações, consulte [as regiões de serviço da Fala.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)

Em seguida, adicione os cabeçalhos necessários para o pedido. Finalmente, fará um pedido ao serviço. Se o pedido for bem sucedido, e um código de estado de 200 for devolvido, a resposta é escrita para arquivar.

Copie este `GetVoices` código para a classe:

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

Está quase concluído. O último passo é instantaneamente a sua aula e chamar as suas funções.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = GetVoices(subscription_key)
    app.get_token()
    app.get_voices()
```

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Está pronto para analisar a amostra. A partir da linha de comando (ou sessão terminal), navegue até ao seu diretório de projeto e corra:

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Limpar recursos

Certifique-se de remover quaisquer informações confidenciais do código fonte da sua aplicação de amostra, como as chaves de subscrição.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore as amostras python no GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Consulte também

* [Referência da API de conversão de texto em voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Criação de fontes de voz personalizadas](how-to-customize-voice-font.md)
* [Gravar amostras de voz para criar uma voz personalizada](record-custom-voice-samples.md)
