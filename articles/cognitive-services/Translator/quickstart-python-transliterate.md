---
title: 'Início rápido: Transcrição de texto, Python - API de texto do tradutor'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, vai aprender a transliterar (converter) texto a partir de um script para outro com o Python e a API REST de Texto do Microsoft Translator. Neste exemplo, o japonês é transliterado para utilizar o alfabeto latino.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: b30fa9bb0af8da7b3c72fef7aa2ecd18a8f759e8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57862850"
---
# <a name="quickstart-use-the-translator-text-api-to-transliterate-text-using-python"></a>Início rápido: Utilizar a API de texto do Translator a transcrição de texto com o Python

Neste início rápido, vai aprender a transliterar (converter) texto a partir de um script para outro com o Python e a API REST de Texto do Microsoft Translator. No exemplo apresentado, o japonês é transliterado para utilizar o alfabeto latino.

Este início rápido requer uma [conta dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de Tradução de Texto. Se não tiver uma conta, pode utilizar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de subscrição.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Python 2.7.x ou 3.x
* Uma chave de subscrição do Azure para Tradução de Texto

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos exigidos

Criar um novo projeto usando o seu IDE favorito ou editor ou uma nova pasta com um arquivo chamado `transliterate-text.py` no ambiente de trabalho. Em seguida, copie este fragmento de código no seu projeto/ficheiro:

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Se ainda não utilizou estes módulos, terá de instalá-los antes de executar o seu programa. Para instalar estes pacotes, execute: `pip install requests uuid`.

O primeiro comentário indica ao interpretador Python para utilizar a codificação UTF-8. Em seguida, os módulos exigidos são importados para ler a chave de subscrição a partir de uma variável de ambiente, construir o pedido de http, criar um identificador exclusivo e processar a resposta JSON devolvida pela API de Texto do Microsoft Translator.

## <a name="set-the-subscription-key-base-url-and-path"></a>Definir a chave de subscrição, o URL base e o caminho

Este exemplo irá tentar ler a chave de subscrição de Tradução de Texto a partir da variável de ambiente `TRANSLATOR_TEXT_KEY`. Se não estiver familiarizado com variáveis de ambiente, pode definir `subscriptionKey` como cadeia e comentar a instrução condicional.

Copie este código para o seu projeto:

```python
# Checks to see if the Translator Text subscription key is available
# as an environment variable. If you are setting your subscription key as a
# string, then comment these lines out.
if 'TRANSLATOR_TEXT_KEY' in os.environ:
    subscriptionKey = os.environ['TRANSLATOR_TEXT_KEY']
else:
    print('Environment variable for TRANSLATOR_TEXT_KEY is not set.')
    exit()
# If you want to set your subscription key as a string, uncomment the line
# below and add your subscription key.
#subscriptionKey = 'put_your_key_here'
```

O ponto final global do texto do tradutor está definido como o `base_url`. `path` define a rota `transliterate` e identifica que queremos utilizar a versão 3 da API.

Os `params` são utilizados para definir o idioma de entrada e os scripts de entrada e saída. Neste exemplo, estamos a transliterar de japonês para o alfabeto latino.

>[!NOTE]
> Para obter mais informações sobre pontos de extremidade, rotas e parâmetros do pedido, consulte [3.0 de API de texto de tradutor: Translitere](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate).

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/transliterate?api-version=3.0'
params = '&language=ja&fromScript=jpan&toScript=latn'
constructed_url = base_url + path + params
```

## <a name="add-headers"></a>Adicionar cabeçalhos

A forma mais fácil de autenticar um pedido é transmitir a sua chave de subscrição como um cabeçalho `Ocp-Apim-Subscription-Key`, que é o que vamos utilizar neste exemplo. Como alternativa, pode trocar a chave de subscrição por um token de acesso e transmiti-lo como um cabeçalho `Authorization` para validar o pedido. Para obter mais informações, veja [Autenticação](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

Copie este código para o seu projeto:

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

## <a name="create-a-request-to-transliterate-text"></a>Criar um pedido para transliterar texto

Defina a cadeia (ou cadeias) que quer transliterar:

```python
# Transliterate "good afternoon" from source Japanese.
# Note: You can pass more than one object in body.
body = [{
    'text': 'こんにちは'
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
print(json.dumps(response, sort_keys=True, indent=4, ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Juntar tudo

Já está. Utilizámos um programa simples que irá chamar a API de Texto do Microsoft Translator e devolver uma resposta JSON. Agora, é altura de executar o seu programa:

```console
python transliterate-text.py
```

Se quiser comparar o seu código com o nosso, o exemplo completo está disponível no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Resposta de amostra

```json
[
    {
        "script": "latn",
        "text": "konnichiwa"
    }
]
```

## <a name="clean-up-resources"></a>Limpar recursos

Se codificou a chave de subscrição no seu programa, certifique-se de que remove a chave de subscrição quando tiver terminado este início rápido.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explorar exemplos do Python no GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python)

## <a name="see-also"></a>Consulte também

Saiba como utilizar a API de texto do Translator para:

* [Traduzir texto](quickstart-python-translate.md)
* [Identificar o idioma por entrada](quickstart-python-detect.md)
* [Obter traduções alternativas](quickstart-python-dictionary.md)
* [Obter uma lista de idiomas suportados](quickstart-python-languages.md)
* [Determinar o comprimento das frases a partir de uma entrada](quickstart-python-sentences.md)
