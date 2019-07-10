---
title: 'Início rápido: Obter lista de idiomas suportados, node. js - API de texto do tradutor'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, irá obter uma lista de idiomas com suporte para tradução, transliteração e pesquisa no dicionário, e exemplos que utilizam a API de Texto do Microsoft Translator com Node.js.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e31f30373349d4048f9021ab8eee7f39dcf5cd57
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705515"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-with-nodejs"></a>Início rápido: Utilizar a API de texto do Translator para obter uma lista de idiomas suportados com node. js

Neste início rápido, irá aprender a fazer um pedido GET que devolve uma lista de idiomas suportados com o Node.js e a API REST de Texto do Microsoft Translator.

>[!TIP]
> Se gostaria de ver todo o código de uma só vez, o código-fonte para este exemplo está disponível no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Node 8.12.x ou posterior](https://nodejs.org/en/)

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos exigidos

Crie um novo projeto através do seu editor ou IDE favorito. Em seguida, copie este fragmento de código para o seu projeto num ficheiro com o nome `get-languages.js`.

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Se ainda não utilizou estes módulos, terá de instalá-los antes de executar o seu programa. Para instalar estes pacotes, execute: `npm install request uuidv4`.

Estes módulos são obrigatórios para construir o pedido HTTP e criar um identificador exclusivo para o cabeçalho `'X-ClientTraceId'`.

## <a name="configure-the-request"></a>Configurar o pedido

O método `request()`, disponibilizado através do módulo de pedido, permite-nos passar o método HTTP, o URL, os parâmetros de pedido, os cabeçalhos e o corpo de JSON como um objeto de `options`. Neste fragmento de código, vamos configurar o pedido:

>[!NOTE]
> Para obter mais informações sobre pontos de extremidade, rotas e parâmetros do pedido, consulte [3.0 de API de texto de tradutor: Idiomas](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

```javascript
let options = {
    method: 'GET',
    baseUrl: 'https://api.cognitive.microsofttranslator.com/',
    url: 'languages',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    json: true,
};
```

Se estiver a utilizar uma subscrição de múltiplos serviço de serviços cognitivos, também tem de incluir o `Ocp-Apim-Subscription-Region` nos parâmetros do pedido. [Saiba mais sobre autenticação com a subscrição de múltiplos serviço](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="make-the-request-and-print-the-response"></a>Fazer o pedido e imprimir a resposta

Em seguida, vamos criar o pedido com o método `request()`. É utilizado o objeto `options` que criámos na secção anterior, como o primeiro argumento e, em seguida, imprime a resposta JSON embelezada.

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> Neste exemplo, vamos definir o pedido HTTP no objeto `options`. No entanto, o módulo de pedido também suporta métodos convenientes, como `.post` e `.get`. Para obter mais informações, veja [métodos convenientes](https://github.com/request/request#convenience-methods).

## <a name="put-it-all-together"></a>Juntar tudo

Já está. Utilizámos um programa simples que irá chamar a API de Texto do Microsoft Translator e devolver uma resposta JSON. Agora, é altura de executar o seu programa:

```console
node get-languages.js
```

Se quiser comparar o seu código com o nosso, o exemplo completo está disponível no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="sample-response"></a>Resposta de amostra

Encontre a abreviatura de país/região desta [lista de idiomas](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

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

Veja a referência de API para compreender tudo o que pode fazer com a API de texto do Translator.

> [!div class="nextstepaction"]
> [Referência da API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="see-also"></a>Consulte também

Além da deteção de idioma, saiba como utilizar a API de Texto do Microsoft Translator para:

* [Traduzir texto](quickstart-nodejs-translate.md)
* [Transliterar texto](quickstart-nodejs-transliterate.md)
* [Identificar o idioma por entrada](quickstart-nodejs-detect.md)
* [Obter traduções alternativas](quickstart-nodejs-dictionary.md)
* [Determinar o comprimento das frases a partir de uma entrada](quickstart-nodejs-sentences.md)
