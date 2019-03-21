---
title: 'Início rápido: Obter lista de idiomas suportados, Python - API de texto do tradutor'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, vai obter uma lista de idiomas com suporte para tradução, transliteração e pesquisa no dicionário, e exemplos que utilizam a API de Texto do Microsoft Translator com o Python.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: e455b0167810c6e08853f3bce665cb6f314eec52
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181457"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-using-python"></a>Início rápido: Utilize a API de texto do Translator para obter uma lista de idiomas suportados com o Python

Neste início rápido, irá aprender a fazer um pedido GET que devolve uma lista de idiomas suportados com o Python e a API REST de Texto do Microsoft Translator.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Python 2.7.x ou 3.x

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos exigidos

Crie um novo projeto do Python através do seu editor ou IDE favorito. Em seguida, copie este fragmento de código para o seu projeto num ficheiro com o nome `get-languages.py`.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Se ainda não utilizou estes módulos, terá de instalá-los antes de executar o seu programa. Para instalar estes pacotes, execute: `pip install requests uuid`.

O primeiro comentário indica ao interpretador Python para utilizar a codificação UTF-8. Em seguida, os módulos exigidos são importados para ler a chave de subscrição a partir de uma variável de ambiente, construir o pedido de http, criar um identificador exclusivo e processar a resposta JSON devolvida pela API de Texto do Microsoft Translator.

## <a name="set-the-base-url-and-path"></a>Definir o base url e o caminho

O ponto final global do texto do tradutor está definido como o `base_url`. `path` define a rota `languages` e identifica que queremos utilizar a versão 3 da API.

>[!NOTE]
> Para obter mais informações sobre pontos de extremidade, rotas e parâmetros do pedido, consulte [3.0 de API de texto de tradutor: Idiomas](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/languages?api-version=3.0'
constructed_url = base_url + path
```

## <a name="add-headers"></a>Adicionar cabeçalhos

O pedido para obter os idiomas suportados não exige a autenticação. Definir o `Content-type` como `application/json` e adicione `X-ClientTraceId` para identificar exclusivamente o seu pedido.

Copie este código para o seu projeto:

```python
headers = {
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

## <a name="create-a-request-to-get-a-list-of-supported-languages"></a>Criar um pedido para obter uma lista de idiomas suportados

Vamos criar um pedido GET com o módulo `requests`. Utiliza dois argumentos: o URL concatenado e os cabeçalhos do pedido:

```python
request = requests.get(constructed_url, headers=headers)
response = request.json()
```

## <a name="print-the-response"></a>Imprimir a resposta

O último passo é imprimir os resultados. Este fragmento de código simplifica os resultados ao ordenar as chaves, definir o avanço e declarar separadores de itens e chaves.

```python
print(json.dumps(response, sort_keys=True, indent=4, ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Juntar tudo

Já está. Utilizámos um programa simples que irá chamar a API de Texto do Microsoft Translator e devolver uma resposta JSON. Agora, é altura de executar o seu programa:

```console
python get-languages.py
```

Se quiser comparar o seu código com o nosso, o exemplo completo está disponível no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Resposta de amostra

Encontre a abreviatura de país nesta [lista de idiomas](https://docs.microsoft.com/en-us/azure/cognitive-services/translator/language-support).

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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explorar exemplos do Python no GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python)

## <a name="see-also"></a>Consulte também

Saiba como utilizar a API de texto do Translator para:

* [Traduzir texto](quickstart-python-translate.md)
* [Transliterar texto](quickstart-python-transliterate.md)
* [Identificar o idioma por entrada](quickstart-python-detect.md)
* [Obter traduções alternativas](quickstart-python-dictionary.md)
* [Determinar o comprimento das frases a partir de uma entrada](quickstart-python-sentences.md)
