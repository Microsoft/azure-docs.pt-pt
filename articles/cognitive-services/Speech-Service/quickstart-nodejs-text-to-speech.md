---
title: 'Quickstart: Converter texto-a-fala, Node.js - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, aprenderá a converter texto-a-fala usando o Node.js e a API do REST Text-to-Speech. O texto da amostra incluído neste guia é estruturado como Linguagem de Marcação da Síntese da Fala (SSML). Isto permite-lhe escolher a voz e a linguagem da resposta da fala.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/13/2020
ms.author: erhopf
ms.openlocfilehash: 8cf9641a1b7a5d1aada13522d612458d5032f883
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258698"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Quickstart: Converter texto-a-fala usando Node.js

Neste arranque rápido, aprenderá a converter texto-a-fala usando o Node.js e a API REST text-to-speech. O corpo de pedido neste guia é estruturado como Linguagem de Marcação de Síntese de [Fala (SSML),](speech-synthesis-markup.md)que lhe permite escolher a voz e a linguagem da resposta.

Este quickstart requer uma [conta de Serviços Cognitivos Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de serviço da Fala. Se não tiver uma conta, pode utilizar a [avaliação gratuita](get-started.md) para obter uma chave de subscrição.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* <a href="https://nodejs.org/en/" target="_blank">Nó 8.12.x ou mais tarde<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* Estúdio Visual , <a href="https://code.visualstudio.com/download" target="_blank">Código <span class="docon docon-navigate-external x-hidden-focus"> </span>de Estúdio Visual, </a>ou o seu editor de texto favorito <a href="https://visualstudio.microsoft.com/downloads/" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>
* Uma chave de subscrição Azure para o serviço De Fala. [Pegue um de graça!](get-started.md)

## <a name="create-a-project-and-require-dependencies"></a>Criar um projeto e exigir dependências

Crie um novo projeto Node.js usando o seu IDE favorito ou editor. Em seguida, copie este fragmento de código para o seu projeto num ficheiro com o nome `tts.js`.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write synthesized speech to a file
const fs = require('fs');
// Requires readline-sync to read command line inputs
const readline = require('readline-sync');
// Requires xmlbuilder to build the SSML body
const xmlbuilder = require('xmlbuilder');
```

> [!NOTE]
> Se ainda não utilizou estes módulos, terá de instalá-los antes de executar o seu programa. Para instalar estes pacotes, execute: `npm install request request-promise xmlbuilder readline-sync`.

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

Na próxima secção, criaremos a função de chamar a API de texto-a-fala e salvar a resposta sintetizada da fala.

## <a name="make-a-request-and-save-the-response"></a>Faça um pedido e poupe a resposta

Aqui vai supor o pedido para a API de texto para discurso e salvar a resposta da fala. Esta amostra pressupõe que estás a usar o ponto final dos EUA Ocidentais. Se o seu recurso estiver registado numa região diferente, certifique-se de que atualiza o `uri`. Para mais informações, consulte [as regiões de serviço da Fala.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)

Em seguida, você precisa adicionar cabeçalhos necessários para o pedido. Certifique-se de `User-Agent` que atualiza com o nome do seu recurso `X-Microsoft-OutputFormat` (localizado no portal Azure) e que se desloque para a sua saída de áudio preferida. Para obter uma lista completa dos formatos de saída, consulte [as saídas de áudio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Em seguida, construa o corpo de pedido usando a linguagem de marcação da síntese da fala (SSML). Esta amostra define a estrutura `text` e utiliza a entrada que criou anteriormente.

>[!NOTE]
> Esta amostra `JessaRUS` utiliza a fonte de voz. Para obter uma lista completa da Microsoft forneceu vozes/idiomas, consulte [o suporte do Idioma](language-support.md).
> Se está interessado em criar uma voz única e reconhecível para a sua marca, consulte Criar fontes de [voz personalizadas.](how-to-customize-voice-font.md)

Finalmente, fará um pedido ao serviço. Se o pedido for bem sucedido, e um código de estado `TTSOutput.wav`de 200 for devolvido, a resposta da fala é escrita como .

```javascript
// Make sure to update User-Agent with the name of your resource.
// You can also change the voice and output formats. See:
// https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech
function textToSpeech(accessToken, text) {
    // Create the SSML request.
    let xml_body = xmlbuilder.create('speak')
        .att('version', '1.0')
        .att('xml:lang', 'en-us')
        .ele('voice')
        .att('xml:lang', 'en-us')
        .att('name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
        .txt(text)
        .end();
    // Convert the XML into a string to send in the TTS request.
    let body = xml_body.toString();

    let options = {
        method: 'POST',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/v1',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'cache-control': 'no-cache',
            'User-Agent': 'YOUR_RESOURCE_NAME',
            'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
            'Content-Type': 'application/ssml+xml'
        },
        body: body
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('TTSOutput.wav'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Juntar tudo

Está quase concluído. O último passo é criar uma função assíncrona. Esta função irá ler a sua chave de subscrição a partir de uma variável ambiental, solicitar texto, obter um símbolo, esperar que o pedido seja concluído, em seguida, converter o texto-a-falar e guardar o áudio como um .wav.

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
    // Prompts the user to input text.
    const text = readline.question('What would you like to convert to speech? ');

    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken, text);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Execute a aplicação de exemplo

Está pronto para executar a sua aplicação de amostra sms-to-speech. A partir da linha de comando (ou sessão terminal), navegue até ao seu diretório de projeto e corra:

```console
node tts.js
```

Quando for solicitado, escreva o que quiser converter de texto para discurso. Se for bem sucedido, o ficheiro de fala está localizado na pasta do projeto. Jogue usando o seu leitor de mídia favorito.

## <a name="clean-up-resources"></a>Limpar recursos

Certifique-se de remover quaisquer informações confidenciais do código fonte da sua aplicação de amostra, como as chaves de subscrição.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore amostras de Node.js no GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Consulte também

* [Referência da API de conversão de texto em voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Criação de fontes de voz personalizadas](how-to-customize-voice-font.md)
* [Gravar amostras de voz para criar uma voz personalizada](record-custom-voice-samples.md)
