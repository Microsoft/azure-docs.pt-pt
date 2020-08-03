---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.custom: devx-track-javascript
ms.openlocfilehash: c6ca405a6e14f92ab0b09d8f277c99c0c51288bf
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87404633"
---
[!INCLUDE [Prerequisites](prerequisites-nodejs.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos exigidos

Crie um novo projeto utilizando o seu IDE ou editor favorito, ou crie uma nova pasta no seu ambiente de trabalho. Copie este corte de código no seu projeto/pasta num ficheiro chamado `alt-translations.js` .

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Se ainda não utilizou estes módulos, terá de instalá-los antes de executar o seu programa. Para instalar estes pacotes, execute: `npm install request uuidv4`.

Estes módulos são obrigatórios para construir o pedido HTTP e criar um identificador exclusivo para o cabeçalho `'X-ClientTraceId'`.

## <a name="set-the-subscription-key-and-endpoint"></a>Definir a chave de subscrição e o ponto final

Esta amostra tentará ler a sua chave de subscrição do Tradutor e ponto final a partir destas variáveis ambientais: `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` e `TRANSLATOR_TEXT_ENDPOINT` . Se não estiver familiarizado com variáveis ambientais, pode definir `subscriptionKey` e como cordas e comentar as `endpoint` declarações condicionais.

Copie este código para o seu projeto:

```javascript
var key_var = 'TRANSLATOR_TEXT_SUBSCRIPTION_KEY';
if (!process.env[key_var]) {
    throw new Error('Please set/export the following environment variable: ' + key_var);
}
var subscriptionKey = process.env[key_var];
var endpoint_var = 'TRANSLATOR_TEXT_ENDPOINT';
if (!process.env[endpoint_var]) {
    throw new Error('Please set/export the following environment variable: ' + endpoint_var);
}
var endpoint = process.env[endpoint_var];
```

## <a name="configure-the-request"></a>Configurar o pedido

O método `request()`, disponibilizado através do módulo de pedido, permite-nos passar o método HTTP, o URL, os parâmetros de pedido, os cabeçalhos e o corpo de JSON como um objeto de `options`. Neste fragmento de código, vamos configurar o pedido:

>[!NOTE]
> Para obter mais informações sobre pontos finais, rotas e parâmetros de pedido, consulte [o Tradutor 3.0: Dictionary Lookup](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-dictionary-lookup).

```javascript
let options = {
    method: 'POST',
    baseUrl: endpoint,
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

A forma mais fácil de autenticar um pedido é transmitir a sua chave de subscrição como um cabeçalho `Ocp-Apim-Subscription-Key`, que é o que vamos utilizar neste exemplo. Como alternativa, pode trocar a chave de subscrição por um token de acesso e transmiti-lo como um cabeçalho `Authorization` para validar o pedido.

Se estiver a utilizar uma subscrição multi-serviço dos Serviços Cognitivos, também deve incluir os `Ocp-Apim-Subscription-Region` cabeçalhos de pedido no seu pedido.

Para obter mais informações, veja [Autenticação](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

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

É isso, juntaste um programa simples que vai ligar ao Tradutor e devolver uma resposta do JSON. Agora, é altura de executar o seu programa:

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

## <a name="next-steps"></a>Passos seguintes

Veja a referência da API para entender tudo o que pode fazer com o Tradutor.

> [!div class="nextstepaction"]
> [Referência da API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
