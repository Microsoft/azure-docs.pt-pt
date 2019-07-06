---
title: 'Início rápido: Lista de texto para discurso vozes, Python - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, irá aprender como obter a lista completa de vozes padrão e neurais para um região/ponto de extremidade com o Python. A lista é retornada como JSON e a disponibilidade de voz varia por região.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 46595cdf665158d019c9b6def19ff6609db803bc
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67602978"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-python"></a>Início rápido: Obter a lista de texto para discurso vozes com Python

Neste início rápido, irá aprender como obter a lista completa de vozes padrão e neurais para um região/ponto de extremidade com o Python. A lista é retornada como JSON e a disponibilidade de voz varia por região. Para obter uma lista de regiões suportadas, consulte [regiões](regions.md).

Este início rápido requer uma [conta dos serviços cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de serviços de voz. Se não tiver uma conta, pode utilizar a [avaliação gratuita](get-started.md) para obter uma chave de subscrição.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Python 2.7.x ou 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), ou no seu editor de texto favorito
* Uma chave de subscrição do Azure para os serviços de voz

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos exigidos

Crie um novo projeto do Python através do seu editor ou IDE favorito. Em seguida, copie este fragmento de código para o seu projeto num ficheiro com o nome `get-voices.py`.

```python
import requests
```

> [!NOTE]
> Se ainda não utilizou estes módulos, terá de instalá-los antes de executar o seu programa. Para instalar estes pacotes, execute: `pip install requests`.

Pedidos é utilizado para pedidos HTTP para o serviço de texto para voz.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Defina a chave de subscrição e criar uma linha de comandos para TTS

As próximas seções irá criar métodos para processar autorizações, chamar a API de texto para voz e validar a resposta. Vamos começar por criar uma classe. Isso é onde, colocamos nossa métodos para troca de token e chamar a API de texto para voz.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

O `subscription_key` é a chave exclusiva do portal do Azure.

## <a name="get-an-access-token"></a>Obter um token de acesso

Este ponto final requer um token de acesso para a autenticação. Para obter um token de acesso, um exchange é necessário. Este exemplo troca sua chave de subscrição de serviços de voz para um token de acesso através do `issueToken` ponto final.

Este exemplo parte do princípio de que a sua subscrição de serviços de voz está na região E.U.A. oeste. Se estiver a utilizar uma região diferente, atualize o valor para `fetch_token_url`. Para obter uma lista completa, consulte [regiões](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Copie este código para o `GetVoices` classe:

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

Aqui pretende criar o pedido e guardar a lista de vozes retornados. Em primeiro lugar, tem de definir o `base_url` e `path`. Este exemplo parte do princípio de que está a utilizar o ponto de extremidade do E.U.A. oeste. Se o recurso está registado para uma região diferente, certifique-se de que atualiza o `base_url`. Para obter mais informações, consulte [regiões de serviços de voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Em seguida, adicione os cabeçalhos necessários para o pedido. Por fim, vou fazer um pedido para o serviço. Se a solicitação for bem-sucedida, e é devolvido um código de 200 Estado, a resposta é escrita no ficheiro.

Copie este código para o `GetVoices` classe:

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

Está quase concluído. A última etapa é instanciar sua classe e chamar as suas funções.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = GetVoices(subscription_key)
    app.get_token()
    app.get_voices()
```

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

É isso, está pronto para executar o exemplo. A partir da linha de comandos (ou sessão de terminal), navegue para o diretório de projeto e execute:

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Limpar recursos

Não se esqueça de remover quaisquer informações confidenciais do código-fonte da sua aplicação de exemplo, como chaves de subscrição.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explorar amostras de Python no GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Consulte também

* [Referência da API de conversão de texto em voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Criar tipos de voz personalizada](how-to-customize-voice-font.md)
* [Exemplos de voz de registo para criar uma voz personalizada](record-custom-voice-samples.md)
