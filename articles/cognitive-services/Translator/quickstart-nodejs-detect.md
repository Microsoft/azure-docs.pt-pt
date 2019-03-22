---
title: 'Início rápido: Detetar o idioma de texto, node. js - API de texto do tradutor'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, irá aprender a identificar o idioma do texto fornecido com o Node.js e a API REST de Texto do Microsoft Translator.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: 35b6a55a3f5cad27045694231fc5ce79ed7aeadf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58173945"
---
# <a name="quickstart-use-the-translator-text-api-to-detect-text-language-with-nodejs"></a>Início rápido: Utilizar a API de texto do Translator para detetar o idioma de texto com node. js

Neste início rápido, irá aprender a detetar o idioma do texto fornecido com o Node.js e a API REST de Texto do Microsoft Translator.

Este início rápido requer uma [conta dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de Tradução de Texto. Se não tiver uma conta, pode utilizar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de subscrição.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Node 8.12.x ou posterior](https://nodejs.org/en/)
* Uma chave de subscrição do Azure para Tradução de Texto

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos exigidos

Crie um novo projeto através do seu editor ou IDE favorito. Em seguida, copie este fragmento de código para o seu projeto num ficheiro com o nome `detect.js`.

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Se ainda não utilizou estes módulos, terá de instalá-los antes de executar o seu programa. Para instalar estes pacotes, execute: `npm install request uuidv4`.

Estes módulos são obrigatórios para construir o pedido HTTP e criar um identificador exclusivo para o cabeçalho `'X-ClientTraceId'`.

## <a name="set-the-subscription-key"></a>Definir a chave de subscrição

Este código irá tentar ler a chave de subscrição de Tradução de Texto a partir da variável de ambiente `TRANSLATOR_TEXT_KEY`. Se não estiver familiarizado com variáveis de ambiente, pode definir `subscriptionKey` como cadeia e comentar a instrução condicional.

Copie este código para o seu projeto:

```javascript
/* Checks to see if the subscription key is available
as an environment variable. If you are setting your subscription key as a
string, then comment these lines out.

If you want to set your subscription key as a string, replace the value for
the Ocp-Apim-Subscription-Key header as a string. */
const subscriptionKey = process.env.TRANSLATOR_TEXT_KEY;
if (!subscriptionKey) {
  throw new Error('Environment variable for your subscription key is not set.')
};
```

## <a name="configure-the-request"></a>Configurar o pedido

O método `request()`, disponibilizado através do módulo de pedido, permite-nos passar o método HTTP, o URL, os parâmetros de pedido, os cabeçalhos e o corpo de JSON como um objeto de `options`. Neste fragmento de código, vamos configurar o pedido:

>[!NOTE]
> Para obter mais informações sobre pontos de extremidade, rotas e parâmetros do pedido, consulte [3.0 de API de texto de tradutor: Detect](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect).

```javascript
let options = {
    method: 'POST',
    baseUrl: 'https://api.cognitive.microsofttranslator.com/',
    url: 'detect',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'Salve, mondo!'
    }],
    json: true,
};
```

### <a name="authentication"></a>Authentication

A forma mais fácil de autenticar um pedido é transmitir a sua chave de subscrição como um cabeçalho `Ocp-Apim-Subscription-Key`, que é o que vamos utilizar neste exemplo. Como alternativa, pode trocar a chave de subscrição por um token de acesso e transmiti-lo como um cabeçalho `Authorization` para validar o pedido. Para obter mais informações, veja [Autenticação](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

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
node detect.js
```

Se quiser comparar o seu código com o nosso, o exemplo completo está disponível no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="sample-response"></a>Resposta de amostra

Encontre a abreviatura de país nesta [lista de idiomas](https://docs.microsoft.com/en-us/azure/cognitive-services/translator/language-support).

```json
[
    {
        "alternatives": [
            {
                "isTranslationSupported": true,
                "isTransliterationSupported": false,
                "language": "pt",
                "score": 1.0
            },
            {
                "isTranslationSupported": true,
                "isTransliterationSupported": false,
                "language": "en",
                "score": 1.0
            }
        ],
        "isTranslationSupported": true,
        "isTransliterationSupported": false,
        "language": "it",
        "score": 1.0
    }
]
```

## <a name="clean-up-resources"></a>Limpar recursos

Se codificou a chave de subscrição no seu programa, certifique-se de que remove a chave de subscrição quando tiver terminado este início rápido.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explorar exemplos de Node.js no GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS)

## <a name="see-also"></a>Consulte também

Além da deteção de idioma, saiba como utilizar a API de Texto do Microsoft Translator para:

* [Traduzir texto](quickstart-nodejs-translate.md)
* [Transliterar texto](quickstart-nodejs-transliterate.md)
* [Obter traduções alternativas](quickstart-nodejs-dictionary.md)
* [Obter uma lista de idiomas suportados](quickstart-nodejs-languages.md)
* [Determinar o comprimento das frases a partir de uma entrada](quickstart-nodejs-sentences.md)
