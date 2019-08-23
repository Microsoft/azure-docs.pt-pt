---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 7f14a12d5de64206f64e8c7205beb2c59c4f1f2a
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906905"
---
[!INCLUDE [Prerequisites](prerequisites-python.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos exigidos

Crie um novo projeto do Python através do seu editor ou IDE favorito. Em seguida, copie este fragmento de código para o seu projeto num ficheiro com o nome `get-languages.py`.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Se ainda não utilizou estes módulos, terá de instalá-los antes de executar o seu programa. Para instalar estes pacotes, execute: `pip install requests uuid`.

O primeiro comentário indica ao interpretador Python para utilizar a codificação UTF-8. Em seguida, os módulos exigidos são importados para ler a chave de subscrição a partir de uma variável de ambiente, construir o pedido de http, criar um identificador exclusivo e processar a resposta JSON devolvida pela API de Texto do Microsoft Translator.

## <a name="set-the-endpoint-and-path"></a>Definir o ponto de extremidade e o caminho

Este exemplo tentará ler seu ponto de extremidade Tradução de Texto de uma variável de `TRANSLATOR_TEXT_ENDPOINT`ambiente:. Se não estiver familiarizado com variáveis de ambiente, pode definir `endpoint` como cadeia e comentar a instrução condicional.

```python
endpoint_var_name = 'TRANSLATOR_TEXT_ENDPOINT'
if not endpoint_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(endpoint_var_name))
endpoint = os.environ[endpoint_var_name]
```

O ponto de extremidade global Tradução de Texto é definido `endpoint`como o. `path` define a rota `languages` e identifica que queremos utilizar a versão 3 da API.

>[!NOTE]
> Para obter mais informações sobre pontos de extremidade, rotas e parâmetros de solicitação, [consulte API de tradução de texto 3,0: Idiomas](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

```python
path = '/languages?api-version=3.0'
constructed_url = endpoint + path
```

## <a name="add-headers"></a>Adicionar cabeçalhos

A solicitação para obter os idiomas com suporte não requer autenticação. `Content-type` Defina como `application/json` e Adicione`X-ClientTraceId` para identificar exclusivamente sua solicitação.

Copie este código para o seu projeto:

```python
headers = {
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

Se você estiver usando uma assinatura de vários serviços cognitivas, também deverá incluir o `Ocp-Apim-Subscription-Region` em seus parâmetros de solicitação. [Saiba mais sobre como autenticar com a assinatura de vários serviços](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-request-to-get-a-list-of-supported-languages"></a>Criar um pedido para obter uma lista de idiomas suportados

Vamos criar um pedido GET com o módulo `requests`. Utiliza dois argumentos: o URL concatenado e os cabeçalhos do pedido:

```python
request = requests.get(constructed_url, headers=headers)
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
python get-languages.py
```

Se quiser comparar o seu código com o nosso, o exemplo completo está disponível no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Resposta de amostra

Localize a abreviação de país/região nesta [lista de idiomas](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

Este exemplo foi truncado para mostrar um fragmento do resultado:

```json
{
    "translation": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr"
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl"
        },
        ...
    },
    "transliteration": {
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "scripts": [
                {
                    "code": "Arab",
                    "name": "Arabic",
                    "nativeName": "العربية",
                    "dir": "rtl",
                    "toScripts": [
                        {
                            "code": "Latn",
                            "name": "Latin",
                            "nativeName": "اللاتينية",
                            "dir": "ltr"
                        }
                    ]
                },
                {
                    "code": "Latn",
                    "name": "Latin",
                    "nativeName": "اللاتينية",
                    "dir": "ltr",
                    "toScripts": [
                        {
                            "code": "Arab",
                            "name": "Arabic",
                            "nativeName": "العربية",
                            "dir": "rtl"
                        }
                    ]
                }
            ]
        },
      ...
    },
    "dictionary": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
      ...
    }
}
```

## <a name="clean-up-resources"></a>Limpar recursos

Se codificou a chave de subscrição no seu programa, certifique-se de que remove a chave de subscrição quando tiver terminado este início rápido.

## <a name="next-steps"></a>Passos seguintes

Dê uma olhada na referência da API para entender tudo o que você pode fazer com o API de Tradução de Texto.

> [!div class="nextstepaction"]
> [Referência da API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
