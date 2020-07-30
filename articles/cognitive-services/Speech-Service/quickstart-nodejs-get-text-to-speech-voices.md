---
title: 'Quickstart: Listar vozes texto-a-discurso, Node.js - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, você aprenderá a obter a lista completa de vozes padrão e neurais para uma região/ponto final usando Node.js. A lista é devolvida como JSON, e a disponibilidade de voz varia por região.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/13/2020
ms.author: erhopf
ms.custom: devx-track-javascript
ms.openlocfilehash: d7ec5b386a9e62606a8b46c4e66cea85f8098a83
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406831"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Quickstart: Obtenha a lista de vozes texto-a-discurso usando Node.js

Neste arranque rápido, você aprenderá a obter a lista completa de vozes padrão e neurais para uma região/ponto final usando Node.js. A lista é devolvida como JSON, e a disponibilidade de voz varia por região. Para obter uma lista de regiões apoiadas, consulte [as regiões.](regions.md)

Este quickstart requer uma [conta Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de serviço de fala. Se não tiver uma conta, pode utilizar a [avaliação gratuita](get-started.md) para obter uma chave de subscrição.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* <a href="https://nodejs.org/en/" target="_blank">Nó 8.12.x ou mais tarde<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual <span class="docon docon-navigate-external x-hidden-focus"></span> Studio </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code, <span class="docon docon-navigate-external x-hidden-focus"></span> </a>ou o seu editor de texto favorito
* Uma chave de subscrição Azure para o serviço Speech. [Arranja um de graça!](get-started.md)

## <a name="create-a-project-and-require-dependencies"></a>Criar um projeto e exigir dependências

Crie um novo projeto de Node.js utilizando o seu IDE ou editor favorito. Em seguida, copie este fragmento de código para o seu projeto num ficheiro com o nome `get-voices.js`.

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

A API DE REPOUSO text-to-speech requer um sinal de acesso para autenticação. Para obter um token de acesso, é necessária uma troca. Esta função troca a chave de subscrição do serviço Speech por um token de acesso utilizando o `issueToken` ponto final.

Esta amostra pressupõe que a sua subscrição de serviço de Discurso está na região oeste dos EUA. Se estiver a utilizar uma região diferente, atualize o valor para `uri` . Para obter uma lista completa, consulte [regiões.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)

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
> Para obter mais informações sobre a autenticação, consulte [Authenticate com um token de acesso.](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token)

Na próxima secção, criaremos a função para obter a lista de vozes e guardar a saída JSON para arquivar.

## <a name="make-a-request-and-save-the-response"></a>Faça um pedido e guarde a resposta

Aqui vais construir o pedido e guardar a lista de vozes devolvidas. Esta amostra assume que estás a usar o ponto final dos EUA. Se o seu recurso estiver registado numa região diferente, certifique-se de que atualiza o `uri` . Para mais informações, consulte [as regiões de serviços de fala.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)

Em seguida, adicione os cabeçalhos necessários para o pedido. Finalmente, fará um pedido ao serviço. Se o pedido for bem sucedido e um código de estado de 200 for devolvido, a resposta será escrita para arquivar.

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

Está quase concluído. O último passo é criar uma função assíncronea. Esta função irá ler a sua chave de subscrição a partir de uma variável ambiental, obter um token, esperar que o pedido seja concluído e, em seguida, escrever a resposta JSON para arquivar.

Se não estiver familiarizado com variáveis ambientais ou preferir testar com a sua chave de subscrição codificada como uma corda, substitua `process.env.SPEECH_SERVICE_KEY` a sua chave de subscrição como uma corda.

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

Está pronto para executar a sua aplicação de amostras. A partir da linha de comando (ou sessão terminal), navegue até ao diretório do projeto e corra:

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>Limpar os recursos

Certifique-se de remover qualquer informação confidencial do código fonte da sua aplicação de amostra, como chaves de subscrição.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Explore Node.js amostras no GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Veja também

* [Referência da API de conversão de texto em voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Criando fontes de voz personalizadas](how-to-customize-voice-font.md)
* [Registar amostras de voz para criar uma voz personalizada](record-custom-voice-samples.md)
