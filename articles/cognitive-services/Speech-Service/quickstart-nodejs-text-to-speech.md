---
title: 'Início rápido: converter conversão de texto em fala, Node. js-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você aprenderá a converter conversão de texto em fala usando o Node. js e a API REST de conversão de texto em fala. O texto de exemplo incluído neste guia está estruturado como Speech Synthesis Markup Language (SSML). Isto permite-lhe escolher a voz e o idioma da resposta de voz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 1bfd9f53c54ac8ed374f11da188f2fade715cdfa
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812818"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Início rápido: converter conversão de texto em fala usando node. js

Neste guia de início rápido, você aprenderá a converter conversão de texto em fala usando o Node. js e a API REST de conversão de texto em fala. O corpo do pedido neste guia está estruturado como [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md), que permite-lhe escolher a voz e o idioma da resposta.

Este início rápido requer uma [conta de serviços cognitivas do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de serviço de fala. Se não tiver uma conta, pode utilizar a [avaliação gratuita](get-started.md) para obter uma chave de subscrição.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Node 8.12.x ou posterior](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), ou no seu editor de texto favorito
* Uma chave de assinatura do Azure para o serviço de fala. [Obtenha um gratuitamente!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Criar um projeto e exigir dependências

Crie um novo projeto Node. js usando seu IDE ou editor favorito. Em seguida, copie este fragmento de código para o seu projeto num ficheiro com o nome `tts.js`.

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

Na próxima seção, criaremos a função para chamar a API de conversão de texto em fala e salvar a resposta de fala sintetizada.

## <a name="make-a-request-and-save-the-response"></a>Fazer um pedido e guardar a resposta

Aqui, você vai criar a solicitação para a API de conversão de texto em fala e salvar a resposta de fala. Este exemplo parte do princípio de que está a utilizar o ponto de extremidade do E.U.A. oeste. Se o recurso está registado para uma região diferente, certifique-se de que atualiza o `uri`. Para obter mais informações, consulte [regiões do serviço de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Em seguida, terá de adicionar cabeçalhos necessários para o pedido. Certifique-se de que Atualize `User-Agent` com o nome do recurso (localizado no portal do Azure) e conjunto `X-Microsoft-OutputFormat` para a saída de áudio preferencial. Para obter uma lista completa dos formatos de saída, consulte [saídas de áudio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Em seguida, construa o corpo do pedido com o Speech Synthesis Markup Language (SSML). Este exemplo define a estrutura e utiliza o `text` de entrada que criou anteriormente.

>[!NOTE]
> Este exemplo utiliza o `JessaRUS` tipo de voz. Para uma lista completa de Microsoft fornecidas vozes/idiomas, consulte [suporte de idiomas](language-support.md).
> Se estiver interessado em criar uma voz exclusiva, reconhecível para sua marca, veja [criar tipos de voz personalizada](how-to-customize-voice-font.md).

Por fim, vou fazer um pedido para o serviço. Se a solicitação for bem-sucedida e um código de status 200 for retornado, a resposta de fala será gravada como `TTSOutput.wav`.

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

Está quase concluído. A última etapa é criar uma função assíncrona. Essa função lerá sua chave de assinatura de uma variável de ambiente, solicitará o texto, obterá um token, aguardará a conclusão da solicitação e, em seguida, converterá o texto em fala e salvará o áudio como um. wav.

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

É isso, está pronto para executar a aplicação de exemplo de texto para voz. A partir da linha de comandos (ou sessão de terminal), navegue para o diretório de projeto e execute:

```console
node tts.js
```

Quando lhe for pedido, escreva qualquer coisa que gostaria de converter a síntese de voz. Se tiver êxito, o ficheiro de voz está localizado na pasta do projeto. Reproduzi-lo usando o leitor de multimédia favorito.

## <a name="clean-up-resources"></a>Limpar recursos

Não se esqueça de remover quaisquer informações confidenciais do código-fonte da sua aplicação de exemplo, como chaves de subscrição.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore os exemplos de Node. js no GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Ver também

* [Referência da API de conversão de texto em voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Criar tipos de voz personalizada](how-to-customize-voice-font.md)
* [Exemplos de voz de registo para criar uma voz personalizada](record-custom-voice-samples.md)
