---
title: 'Início rápido: Lista de texto para discurso vozes, node. js - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, irá aprender como obter a lista completa de vozes padrão e neurais para um região/ponto de extremidade com node. js. A lista é retornada como JSON e a disponibilidade de voz varia por região.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: erhopf
ms.openlocfilehash: 62187ddbe587a81038f8424b079e3c0c313d1ae2
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887104"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Início rápido: Obter a lista de texto para discurso vozes com node. js

Neste início rápido, irá aprender como obter a lista completa de vozes padrão e neurais para um região/ponto de extremidade com node. js. A lista é retornada como JSON e a disponibilidade de voz varia por região. Para obter uma lista de regiões suportadas, consulte [regiões](regions.md).

Este início rápido requer uma [conta dos serviços cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de serviços de voz. Se não tiver uma conta, pode utilizar a [avaliação gratuita](get-started.md) para obter uma chave de subscrição.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Nó 8.12.x ou posterior](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), ou no seu editor de texto favorito
* Uma chave de subscrição do Azure para os serviços de voz. [Obter uma gratuitamente! ](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Crie um projeto e necessitam de dependências

Crie um novo projeto de node. js com o seu IDE ou editor favorito. Em seguida, copie este fragmento de código para o seu projeto num ficheiro com o nome `get-voices.js`.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write the list of languagesto a file
const fs = require('fs');
```

> [!NOTE]
> Se ainda não utilizou estes módulos, terá de instalá-los antes de executar o seu programa. Para instalar estes pacotes, execute: `npm install request request-promise`.

## <a name="get-an-access-token"></a>Obter um token de acesso

A API REST do texto para discurso exige um token de acesso para a autenticação. Para obter um token de acesso, um exchange é necessário. Esta função troca sua chave de subscrição de serviços de voz para um token de acesso através do `issueToken` ponto final.

Este exemplo parte do princípio de que a sua subscrição de serviços de voz está na região E.U.A. oeste. Se estiver a utilizar uma região diferente, atualize o valor para `uri`. Para obter uma lista completa, consulte [regiões](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

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

Na próxima seção, vamos criar a função para obter a lista de vozes e guarde a saída JSON ficheiro.

## <a name="make-a-request-and-save-the-response"></a>Fazer um pedido e guardar a resposta

Aqui pretende criar o pedido e guardar a lista de vozes retornados. Este exemplo parte do princípio de que está a utilizar o ponto de extremidade do E.U.A. oeste. Se o recurso está registado para uma região diferente, certifique-se de que atualiza o `uri`. Para obter mais informações, consulte [regiões de serviços de voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Em seguida, adicione os cabeçalhos necessários para o pedido. Por fim, vou fazer um pedido para o serviço. Se a solicitação for bem-sucedida, e é devolvido um código de 200 Estado, a resposta é escrita no ficheiro.

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

Está quase concluído. A última etapa é criar uma função assíncrona. Esta função irá ler a chave de subscrição de uma variável de ambiente, obter um token, aguarde a conclusão da solicitação, em seguida, gravar a resposta JSON para o ficheiro.

Se não estiver familiarizado com as variáveis de ambiente ou prefere testar com a sua subscrição chave embutido em código como uma cadeia de caracteres, substitua `process.env.SPEECH_SERVICE_KEY` com a sua chave de subscrição como uma cadeia de caracteres.

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

É isso, está pronto para executar a aplicação de exemplo. A partir da linha de comandos (ou sessão de terminal), navegue para o diretório de projeto e execute:

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>Limpar recursos

Não se esqueça de remover quaisquer informações confidenciais do código-fonte da sua aplicação de exemplo, como chaves de subscrição.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explore exemplos de node. js no GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Consulte também

* [Referência da API de conversão de texto em voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Criar tipos de voz personalizada](how-to-customize-voice-font.md)
* [Exemplos de voz de registo para criar uma voz personalizada](record-custom-voice-samples.md)
