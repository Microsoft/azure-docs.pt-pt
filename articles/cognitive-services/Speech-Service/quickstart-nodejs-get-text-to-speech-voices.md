---
title: 'Quickstart: Lista de vozes de texto para discurso, Node.js - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, você vai aprender a obter a lista completa de vozes padrão e neurais para uma região/ponto final usando Node.js. A lista é devolvida como JSON, e a disponibilidade de voz varia por região.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: 7a929794ffaea4f863ffaef7227e58c7ccf901f0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74976575"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Quickstart: Obtenha a lista de vozes de texto-a-fala usando Node.js

Neste arranque rápido, você vai aprender a obter a lista completa de vozes padrão e neurais para uma região/ponto final usando Node.js. A lista é devolvida como JSON, e a disponibilidade de voz varia por região. Para obter uma lista de regiões apoiadas, consulte [regiões.](regions.md)

Este quickstart requer uma [conta de Serviços Cognitivos Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de serviço da Fala. Se não tiver uma conta, pode utilizar a [avaliação gratuita](get-started.md) para obter uma chave de subscrição.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Node 8.12.x ou posterior](https://nodejs.org/en/)
* [Estúdio Visual](https://visualstudio.microsoft.com/downloads/), [Código de Estúdio Visual,](https://code.visualstudio.com/download)ou o seu editor de texto favorito
* Uma chave de subscrição Azure para o serviço De Fala. [Pegue um de graça!](get-started.md)

## <a name="create-a-project-and-require-dependencies"></a>Criar um projeto e exigir dependências

Crie um novo projeto Node.js usando o seu IDE favorito ou editor. Em seguida, copie este fragmento de código para o seu projeto num ficheiro com o nome `get-voices.js`.

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

A API REST text-to-speech requer um sinal de acesso para autenticação. Para obter um sinal de acesso, é necessária uma troca. Esta função troca a chave de subscrição `issueToken` do serviço Speech por um token de acesso utilizando o ponto final.

Esta amostra pressupõe que a sua subscrição de serviço speech está na região dos EUA Ocidentais. Se estiver a utilizar uma região `uri`diferente, atualize o valor para . Para obter uma lista completa, consulte [Regiões.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)

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
> Para obter mais informações sobre a autenticação, consulte [Authenticate com um sinal de acesso](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

Na próxima secção, criaremos a função de obter a lista de vozes e salvar a saída jSON para arquivar.

## <a name="make-a-request-and-save-the-response"></a>Faça um pedido e poupe a resposta

Aqui vais construir o pedido e salvar a lista de vozes devolvidas. Esta amostra pressupõe que estás a usar o ponto final dos EUA Ocidentais. Se o seu recurso estiver registado numa região diferente, certifique-se de que atualiza o `uri`. Para mais informações, consulte [as regiões de serviço da Fala.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)

Em seguida, adicione os cabeçalhos necessários para o pedido. Finalmente, fará um pedido ao serviço. Se o pedido for bem sucedido, e um código de estado de 200 for devolvido, a resposta é escrita para arquivar.

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

Está quase concluído. O último passo é criar uma função assíncrona. Esta função irá ler a sua chave de subscrição a partir de uma variável ambiental, obter um símbolo, esperar que o pedido seja concluído, em seguida, escrever a resposta JSON para arquivar.

Se não estiver familiarizado com variáveis ambientais ou preferir testar com a `process.env.SPEECH_SERVICE_KEY` sua chave de subscrição codificada como uma cadeia, substitua-a pela sua chave de subscrição como uma corda.

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

Está pronto para executar a sua aplicação de amostras. A partir da linha de comando (ou sessão terminal), navegue até ao seu diretório de projeto e corra:

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>Limpar recursos

Certifique-se de remover quaisquer informações confidenciais do código fonte da sua aplicação de amostra, como as chaves de subscrição.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore amostras de Node.js no GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Consulte também

* [Referência da API de conversão de texto em voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Criação de fontes de voz personalizadas](how-to-customize-voice-font.md)
* [Gravar amostras de voz para criar uma voz personalizada](record-custom-voice-samples.md)
