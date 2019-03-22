---
title: 'Início rápido: Procure palavras dicionário bilingue, node. js - API de texto do tradutor'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, irá aprender a encontrar traduções alternativas e exemplos de utilização para um texto especificado com o Node.js e a API REST de Texto do Microsoft Translator.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: 4b8d26868c3880852e0d2f9c43b06ca1b25d094f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176383"
---
# <a name="quickstart-look-up-words-with-bilingual-dictionary-using-nodejs"></a>Início rápido: Procure palavras com dicionário bilingue com node. js

Neste início rápido, irá aprender a encontrar traduções alternativas e exemplos de utilização para um texto especificado com o Node.js e a API REST de Texto do Microsoft Translator.

Este início rápido requer uma [conta dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de Tradução de Texto. Se não tiver uma conta, pode utilizar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de subscrição.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Node 8.12.x ou posterior](https://nodejs.org/en/)
* Uma chave de subscrição do Azure para Tradução de Texto

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos exigidos

Criar um novo projeto usando o seu IDE ou editor favorito ou crie uma nova pasta na área de trabalho. Copiar este fragmento de código na pasta/para um ficheiro com o nome do projeto `alt-translations.js`.

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
> Para obter mais informações sobre pontos de extremidade, rotas e parâmetros do pedido, consulte [3.0 de API de texto de tradutor: Pesquisa de dicionário](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-dictionary-lookup).

```javascript
let options = {
    method: 'POST',
    baseUrl: 'https://api.cognitive.microsofttranslator.com/',
    url: 'dictionary/lookup',
    qs: {
      'api-version': '3.0',
      'from': 'en',
      'to': 'es'
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'Elephants'
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
node alt-translations.js
```

Se quiser comparar o seu código com o nosso, o exemplo completo está disponível no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="sample-response"></a>Resposta de amostra

```json
[
    {
        "displaySource": "elephants",
        "normalizedSource": "elephants",
        "translations": [
            {
                "backTranslations": [
                    {
                        "displayText": "elephants",
                        "frequencyCount": 1207,
                        "normalizedText": "elephants",
                        "numExamples": 5
                    }
                ],
                "confidence": 1.0,
                "displayTarget": "elefantes",
                "normalizedTarget": "elefantes",
                "posTag": "NOUN",
                "prefixWord": ""
            }
        ]
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
* [Identificar o idioma por entrada](quickstart-nodejs-detect.md)
* [Obter uma lista de idiomas suportados](quickstart-nodejs-languages.md)
* [Determinar o comprimento das frases a partir de uma entrada](quickstart-nodejs-sentences.md)
