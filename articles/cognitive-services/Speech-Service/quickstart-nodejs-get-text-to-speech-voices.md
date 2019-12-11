---
title: 'Início rápido: listar vozes de conversão de texto em fala, Node. js-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você aprenderá a obter a lista completa de vozes padrão e neurais para uma região/ponto de extremidade usando o Node. js. A lista é retornada como JSON e a disponibilidade de voz varia por região.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: 7a929794ffaea4f863ffaef7227e58c7ccf901f0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976575"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Início rápido: obter a lista de vozes de conversão de texto em fala usando node. js

Neste guia de início rápido, você aprenderá a obter a lista completa de vozes padrão e neurais para uma região/ponto de extremidade usando o Node. js. A lista é retornada como JSON e a disponibilidade de voz varia por região. Para obter uma lista de regiões com suporte, consulte [regiões](regions.md).

Este início rápido requer uma [conta de serviços cognitivas do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de serviço de fala. Se não tiver uma conta, pode utilizar a [avaliação gratuita](get-started.md) para obter uma chave de subscrição.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Node 8.12.x ou posterior](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), ou no seu editor de texto favorito
* Uma chave de assinatura do Azure para o serviço de fala. [Obtenha um gratuitamente!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Criar um projeto e exigir dependências

Crie um novo projeto Node. js usando seu IDE ou editor favorito. Em seguida, copie este fragmento de código para o seu projeto num ficheiro com o nome `get-voices.js`.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write the list of languages to a file
const fs = require('fs');
```

> [!NOTE]
> Se ainda não utilizou estes módulos, terá de instalá-los antes de executar o seu programa. Para instalar estes pacotes, execute: `npm install request request-promise`.

## <a name="get-an-access-token"></a>Obter um token de acesso

A API REST do texto para discurso exige um token de acesso para a autenticação. Para obter um token de acesso, um exchange é necessário. Essa função troca sua chave de assinatura do serviço de fala para um token de acesso usando o ponto de extremidade `issueToken`.

Este exemplo pressupõe que sua assinatura do serviço de fala está na região oeste dos EUA. Se estiver a utilizar uma região diferente, atualize o valor para `uri`. Para obter uma lista completa, consulte [regiões](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Copie este código para o seu projeto:

```javascript
// Gets an access token.
function getAccessToken(subscriptionKey) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    }
    return rp(options);
}
```

> [!NOTE]
> Para obter mais informações sobre autenticação, consulte [autenticar com um token de acesso](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

Na próxima seção, criaremos a função para obter a lista de vozes e salvar a saída JSON no arquivo.

## <a name="make-a-request-and-save-the-response"></a>Fazer um pedido e guardar a resposta

Aqui, você vai criar a solicitação e salvar a lista de vozes retornadas. Este exemplo parte do princípio de que está a utilizar o ponto de extremidade do E.U.A. oeste. Se o recurso está registado para uma região diferente, certifique-se de que atualiza o `uri`. Para obter mais informações, consulte [regiões do serviço de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Em seguida, adicione os cabeçalhos necessários para a solicitação. Por fim, vou fazer um pedido para o serviço. Se a solicitação for bem-sucedida e um código de status 200 for retornado, a resposta será gravada em arquivo.

```javascript
function textToSpeech(accessToken) {
    let options = {
        method: 'GET',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/voices/list',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'Content-Type': 'application/json'
        }
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('voices.json'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Juntar tudo

Está quase concluído. A última etapa é criar uma função assíncrona. Essa função lerá sua chave de assinatura de uma variável de ambiente, obterá um token, aguardará a conclusão da solicitação e gravará a resposta JSON no arquivo.

Se você não estiver familiarizado com variáveis de ambiente ou preferir testar com sua chave de assinatura codificada como uma cadeia de caracteres, substitua `process.env.SPEECH_SERVICE_KEY` pela sua chave de assinatura como uma cadeia de caracteres.

```javascript
// Use async and await to get the token before attempting
// to convert text to speech.
async function main() {
    // Reads subscription key from env variable.
    // You can replace this with a string containing your subscription key. If
    // you prefer not to read from an env variable.
    // e.g. const subscriptionKey = "your_key_here";
    const subscriptionKey = process.env.SPEECH_SERVICE_KEY;
    if (!subscriptionKey) {
        throw new Error('Environment variable for your subscription key is not set.')
    };
    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

É isso, você está pronto para executar seu aplicativo de exemplo. A partir da linha de comandos (ou sessão de terminal), navegue para o diretório de projeto e execute:

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>Limpar recursos

Não se esqueça de remover quaisquer informações confidenciais do código-fonte da sua aplicação de exemplo, como chaves de subscrição.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore os exemplos de Node. js no GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Ver também

* [Referência da API de conversão de texto em voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Criar tipos de voz personalizada](how-to-customize-voice-font.md)
* [Exemplos de voz de registo para criar uma voz personalizada](record-custom-voice-samples.md)
