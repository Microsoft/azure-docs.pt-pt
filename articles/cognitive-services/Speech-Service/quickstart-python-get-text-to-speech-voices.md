---
title: 'Início rápido: listar vozes de conversão de texto em fala, serviço de fala em Python'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você aprenderá a obter a lista completa de vozes padrão e neurais para uma região/ponto de extremidade usando Python. A lista é retornada como JSON e a disponibilidade de voz varia por região.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: b20a8aabbd41c09efb6818cac2999a8c84b669fc
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816436"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-python"></a>Início rápido: obter a lista de vozes de conversão de texto em fala usando Python

Neste guia de início rápido, você aprenderá a obter a lista completa de vozes padrão e neurais para uma região/ponto de extremidade usando Python. A lista é retornada como JSON e a disponibilidade de voz varia por região. Para obter uma lista de regiões com suporte, consulte [regiões](regions.md).

Este início rápido requer uma [conta de serviços cognitivas do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de serviço de fala. Se não tiver uma conta, pode utilizar a [avaliação gratuita](get-started.md) para obter uma chave de subscrição.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Python 2.7.x ou 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), ou no seu editor de texto favorito
* Uma chave de assinatura do Azure para o serviço de fala

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos exigidos

Crie um novo projeto do Python através do seu editor ou IDE favorito. Em seguida, copie este fragmento de código para o seu projeto num ficheiro com o nome `get-voices.py`.

```python
import requests
```

> [!NOTE]
> Se ainda não utilizou estes módulos, terá de instalá-los antes de executar o seu programa. Para instalar estes pacotes, execute: `pip install requests`.

As solicitações são usadas para solicitações HTTP para o serviço de conversão de texto em fala.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Defina a chave de subscrição e criar uma linha de comandos para TTS

As próximas seções irá criar métodos para processar autorizações, chamar a API de texto para voz e validar a resposta. Vamos começar criando uma classe. Isso é onde, colocamos nossa métodos para troca de token e chamar a API de texto para voz.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

O `subscription_key` é a chave exclusiva do portal do Azure.

## <a name="get-an-access-token"></a>Obter um token de acesso

Esse ponto de extremidade requer um token de acesso para autenticação. Para obter um token de acesso, um exchange é necessário. Este exemplo troca sua chave de assinatura do serviço de fala para um token de acesso usando o ponto de extremidade `issueToken`.

Este exemplo pressupõe que sua assinatura do serviço de fala está na região oeste dos EUA. Se estiver a utilizar uma região diferente, atualize o valor para `fetch_token_url`. Para obter uma lista completa, consulte [regiões](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

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

Aqui, você vai criar a solicitação e salvar a lista de vozes retornadas. Em primeiro lugar, tem de definir o `base_url` e `path`. Este exemplo parte do princípio de que está a utilizar o ponto de extremidade do E.U.A. oeste. Se o recurso está registado para uma região diferente, certifique-se de que atualiza o `base_url`. Para obter mais informações, consulte [regiões do serviço de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Em seguida, adicione os cabeçalhos necessários para a solicitação. Por fim, vou fazer um pedido para o serviço. Se a solicitação for bem-sucedida e um código de status 200 for retornado, a resposta será gravada em arquivo.

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

É isso, você está pronto para executar o exemplo. A partir da linha de comandos (ou sessão de terminal), navegue para o diretório de projeto e execute:

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Limpar recursos

Não se esqueça de remover quaisquer informações confidenciais do código-fonte da sua aplicação de exemplo, como chaves de subscrição.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore os exemplos do Python no GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Ver também

* [Referência da API de conversão de texto em voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Criar tipos de voz personalizada](how-to-customize-voice-font.md)
* [Exemplos de voz de registo para criar uma voz personalizada](record-custom-voice-samples.md)
