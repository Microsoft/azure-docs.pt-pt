---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 648842e86410985e3a6fb21f474b9df9d14e109d
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "69906719"
---
[!INCLUDE [Prerequisites](prerequisites-python.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos exigidos

Crie um novo projeto do Python através do seu editor ou IDE favorito. Em seguida, copie este fragmento de código para o seu projeto num ficheiro com o nome `translate-text.py`. Certifique-se de que o intérprete do IDE faz referência à versão correta da Python para evitar que as bibliotecas não sejam reconhecidas.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Se ainda não utilizou estes módulos, terá de instalá-los antes de executar o seu programa. Para instalar estes pacotes, execute: `pip install requests uuid`.

O primeiro comentário indica ao interpretador Python para utilizar a codificação UTF-8. Em seguida, os módulos exigidos são importados para ler a chave de subscrição a partir de uma variável de ambiente, construir o pedido de http, criar um identificador exclusivo e processar a resposta JSON devolvida pela API de Texto do Microsoft Translator.

## <a name="set-the-subscription-key-endpoint-and-path"></a>Desdefinir a chave de subscrição, ponto final e caminho

Esta amostra tentará ler a chave de subscrição de `TRANSLATOR_TEXT_KEY` texto `TRANSLATOR_TEXT_ENDPOINT`tradutor e ponto final das variáveis ambientais: e . Se não está familiarizado com variáveis ambientais, pode definir `subscription_key` e `endpoint` como cordas e comentar as declarações condicionais.

Copie este código para o seu projeto:

```python
key_var_name = 'TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
if not key_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(key_var_name))
subscription_key = os.environ[key_var_name]

endpoint_var_name = 'TRANSLATOR_TEXT_ENDPOINT'
if not endpoint_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(endpoint_var_name))
endpoint = os.environ[endpoint_var_name]
```

O ponto final global do `endpoint`Texto tradutor é definido como o . `path` define a rota `translate` e identifica que queremos utilizar a versão 3 da API.

São utilizados `params` para definir os idiomas de saída. Neste exemplo, vamos traduzir de inglês para italiano e alemão: `it` e `de`.

>[!NOTE]
> Para obter mais informações sobre pontos finais, rotas e parâmetros de pedido, veja [API de Texto do Microsoft Translator 3.0: Traduzir](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate).

```python
path = '/translate?api-version=3.0'
params = '&to=de&to=it'
constructed_url = endpoint + path + params
```

## <a name="add-headers"></a>Adicionar cabeçalhos

A forma mais fácil de autenticar um pedido é transmitir a sua chave de subscrição como um cabeçalho `Ocp-Apim-Subscription-Key`, que é o que vamos utilizar neste exemplo. Como alternativa, pode trocar a chave de subscrição por um token de acesso e transmiti-lo como um cabeçalho `Authorization` para validar o pedido. Para obter mais informações, veja [Autenticação](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

Copie este código para o seu projeto:

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

Se estiver a utilizar uma subscrição multi-serviço `Ocp-Apim-Subscription-Region` de Serviços Cognitivos, também deve incluir os parâmetros do seu pedido. [Saiba mais sobre autenticação com a subscrição de vários serviços.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)

## <a name="create-a-request-to-translate-text"></a>Criar um pedido para traduzir texto

Defina a cadeia (ou cadeias) que quer traduzir:

```python
body = [{
    'text': 'Hello World!'
}]
```

Em seguida, vamos criar um pedido POST com o módulo `requests`. Utiliza três argumentos: o URL concatenado, os cabeçalhos do pedido e o corpo do pedido:

```python
request = requests.post(constructed_url, headers=headers, json=body)
response = request.json()
```

## <a name="print-the-response"></a>Imprimir a resposta

O último passo é imprimir os resultados. Este fragmento de código simplifica os resultados ao ordenar as chaves, definir o avanço e declarar separadores de itens e chaves.

```python
print(json.dumps(response, sort_keys=True, indent=4,
                 ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Juntar tudo

Já está. Utilizámos um programa simples que irá chamar a API de Texto do Microsoft Translator e devolver uma resposta JSON. Agora, é altura de executar o seu programa:

```console
python translate-text.py
```

Se quiser comparar o seu código com o nosso, o exemplo completo está disponível no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Resposta de amostra

```json
[
    {
        "detectedLanguage": {
            "language": "en",
            "score": 1.0
        },
        "translations": [
            {
                "text": "Hallo Welt!",
                "to": "de"
            },
            {
                "text": "Salve, mondo!",
                "to": "it"
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>Limpar recursos

Se codificou a chave de subscrição no seu programa, certifique-se de que remove a chave de subscrição quando tiver terminado este início rápido.

## <a name="next-steps"></a>Passos seguintes

Veja a referência da API para entender tudo o que pode fazer com a API de Texto tradutor.

> [!div class="nextstepaction"]
> [Referência da API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
