---
title: 'Quickstart: Converter texto-a-discurso, Node.js - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, você aprenderá a converter texto-a-discurso usando Node.js e a API text-to-speech REST. O texto da amostra incluído neste guia é estruturado como Linguagem de Marcação de Síntese de Fala (SSML). Isto permite-lhe escolher a voz e a linguagem da resposta da fala.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/13/2020
ms.author: erhopf
ms.custom: devx-track-javascript
ms.openlocfilehash: 38f8df1e721d2e29d4871a05263d9f4559aaedc4
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406814"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Quickstart: Converter texto-a-discurso usando Node.js

Neste arranque rápido, você aprenderá a converter texto-a-discurso usando Node.js e a API DE REPOUSO text-to-speech. O órgão de pedido neste guia é estruturado como Linguagem de Marcação de [Síntese de Fala (SSML),](speech-synthesis-markup.md)que lhe permite escolher a voz e a linguagem da resposta.

Este quickstart requer uma [conta Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de serviço de fala. Se não tiver uma conta, pode utilizar a [avaliação gratuita](get-started.md) para obter uma chave de subscrição.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* <a href="https://nodejs.org/en/" target="_blank">Nó 8.12.x ou mais tarde<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual <span class="docon docon-navigate-external x-hidden-focus"></span> Studio </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code, <span class="docon docon-navigate-external x-hidden-focus"></span> </a>ou o seu editor de texto favorito
* Uma chave de subscrição Azure para o serviço Speech. [Arranja um de graça!](get-started.md)

## <a name="create-a-project-and-require-dependencies"></a>Criar um projeto e exigir dependências

Crie um novo projeto de Node.js utilizando o seu IDE ou editor favorito. Em seguida, copie este fragmento de código para o seu projeto num ficheiro com o nome `tts.js`.

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

Na secção seguinte, criaremos a função de chamar a API de texto-a-discurso e salvar a resposta da fala sintetizada.

## <a name="make-a-request-and-save-the-response"></a>Faça um pedido e guarde a resposta

Aqui vais construir o pedido para a API de texto-a-discurso e salvar a resposta da fala. Esta amostra assume que estás a usar o ponto final dos EUA. Se o seu recurso estiver registado numa região diferente, certifique-se de que atualiza o `uri` . Para mais informações, consulte [as regiões de serviços de fala.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)

Em seguida, tem de adicionar os cabeçalhos necessários para o pedido. Certifique-se de que atualiza `User-Agent` com o nome do seu recurso (localizado no portal Azure) e definido para a sua saída de áudio `X-Microsoft-OutputFormat` preferida. Para obter uma lista completa dos formatos de saída, consulte [as saídas áudio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Em seguida, construa o corpo de pedido usando a linguagem de marcação da síntese da fala (SSML). Esta amostra define a estrutura e utiliza a `text` entrada que criou anteriormente.

>[!NOTE]
> Esta amostra utiliza o `JessaRUS` tipo de letra de voz. Para obter uma lista completa de vozes/idiomas fornecidos pela Microsoft, consulte [o suporte linguístico](language-support.md).
> Se estiver interessado em criar uma voz única e reconhecível para a sua marca, consulte [criar fontes de voz personalizadas.](how-to-customize-voice-font.md)

Finalmente, fará um pedido ao serviço. Se o pedido for bem sucedido e um código de estado de 200 for devolvido, a resposta da fala é escrita como `TTSOutput.wav` .

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

Está quase concluído. O último passo é criar uma função assíncronea. Esta função irá ler a sua chave de subscrição a partir de uma variável ambiental, solicitar texto, obter um token, esperar que o pedido seja concluído, depois converter o texto-a-voz e guardar o áudio como .wav.

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

É isso, estás pronto para executar a tua aplicação de amostra de texto-a-fala. A partir da linha de comando (ou sessão terminal), navegue até ao diretório do projeto e corra:

```console
node tts.js
```

Quando solicitado, digite o que quiser converter de texto para discurso. Se for bem sucedido, o ficheiro de fala está localizado na sua pasta de projeto. Jogue-o usando o seu leitor de media favorito.

## <a name="clean-up-resources"></a>Limpar os recursos

Certifique-se de remover qualquer informação confidencial do código fonte da sua aplicação de amostra, como chaves de subscrição.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Explore Node.js amostras no GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Veja também

* [Referência da API de conversão de texto em voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Criando fontes de voz personalizadas](how-to-customize-voice-font.md)
* [Registar amostras de voz para criar uma voz personalizada](record-custom-voice-samples.md)
