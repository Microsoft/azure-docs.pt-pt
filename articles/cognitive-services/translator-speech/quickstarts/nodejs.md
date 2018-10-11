---
title: 'Início Rápido: Node. js da API de Voz do Microsoft Translator'
titlesuffix: Azure Cognitive Services
description: Obtenha informações e exemplos de código para o ajudar a começar a utilizar rapidamente a API de Voz do Microsoft Translator.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: quickstart
ms.date: 3/5/2018
ms.author: v-jaswel
ROBOTS: NOINDEX
ms.openlocfilehash: 533faeca37f021beb86148a4f11a0f04c631a30a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954590"
---
# <a name="quickstart-translator-speech-api-with-nodejs"></a>Início Rápido: Node. js da API de Voz do Microsoft Translator 
<a name="HOLTop"></a>

Este artigo mostra como utilizar a API de Voz do Microsoft Translator para traduzir linguagem falada num ficheiro .wav.

## <a name="prerequisites"></a>Pré-requisitos

Vai necessitar do [Node.js 6](https://nodejs.org/en/download/) para executar este código.

Tem de instalar o [pacote Websocket ](https://www.npmjs.com/package/websocket) para Node. js.

Necessita de ter um ficheiro . wav denominado "speak.wav" na mesma pasta que o executável e que pode compilar a partir do código abaixo. Este ficheiro . wav deve estar em PCM padrão, em formato mono de 16 bits e 16 kHz. Pode obter esse ficheiro .wav a partir da [API de Conversão de Texto em Voz](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech).

Tem de ter uma [conta de API de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com a **API de Voz do Microsoft Translator**. Tem de ter uma chave de subscrição paga a partir do [dashboard do Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Traduzir voz

O código a seguir converte voz de um idioma para outro.

1. Crie um novo projeto de Node.js no seu IDE preferido.
2. Adicione o código indicado abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua subscrição.
4. Execute o programa.

```nodejs
/* To install this dependency, run:
npm install websocket
*/
var wsClient = require('websocket').client;
var fs = require('fs');
/* To install this dependency, run:
npm install stream-buffers
*/
var streamBuffers = require('stream-buffers');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let key = 'ENTER KEY HERE';

let host = 'wss://dev.microsofttranslator.com';
let path = '/speech/translate';
let params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa';
let uri = host + path + params;

/* The input .wav file is in PCM 16bit, 16kHz, mono format.
You can obtain such a .wav file using the Text to Speech API. See:
https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech
*/
let input_path = 'speak.wav';

let output_path = 'speak2.wav';

function receive(message) {
    console.log ("Received message of type: " + message.type + ".");

    if (message.type == 'utf8') {
        var result = JSON.parse(message.utf8Data)
        console.log("Response type: " + result.type + ".");
        console.log("Recognized input as: " + result.recognition);
        console.log("Translation: " + result.translation);
    }
    else if (message.type == 'binary') {
/* This is needed to make sure message.binaryData is defined. See:
https://stackoverflow.com/questions/17546953/cant-access-object-property-even-though-it-exists-returns-undefined
*/
        let binary_data = JSON.parse (JSON.stringify (message.binaryData));

        if (binary_data.type == 'Buffer') {
/* Put the binary data in a Buffer - do not write it directly to the file. */
            let buffer = new Buffer(binary_data.data, 'binary');
/* Write the contents of the Buffer to the file. */
            fs.writeFile (output_path, buffer, 'binary', function (error) {
/* fs.writeFile calls the error-handling function even if there is no error, in which case error === null. See:
https://stackoverflow.com/questions/44473868/node-js-fs-writefile-err-returns-null
*/
                if (error !== null) {
                    console.log ("Error: " + error);
                }
            });
        }
    }
}

function send(connection, filename) {
    
    var myReadableStreamBuffer = new streamBuffers.ReadableStreamBuffer({
        frequency: 100,
        chunkSize: 32000
    });

/* Make sure the audio file is followed by silence.
This lets the service know that the audio file is finished.
At 32 bytes per millisecond, this is 100 seconds of silence. */
    myReadableStreamBuffer.put(fs.readFileSync(filename));
    myReadableStreamBuffer.put(new Buffer(3200000));
    myReadableStreamBuffer.stop();

    myReadableStreamBuffer.on('data', function (data) {
        connection.sendBytes(data);
    });

    myReadableStreamBuffer.on('end', function () {
        console.log('Closing connection.');
        connection.close(1000);
    });
}

function connect() {
    var ws = new wsClient();

    ws.on('connectFailed', function (error) {
        console.log('Connection error: ' + error.toString());
    });
                            
    ws.on('connect', function (connection) {
        console.log('Connected.');

        connection.on('message', receive);
        
        connection.on('close', function (reasonCode, description) {
            console.log('Connection closed: ' + reasonCode);
        });

        connection.on('error', function (error) {
            console.log('Connection error: ' + error.toString());
        });
        
        send(connection, input_path);
    });

    ws.connect(uri, null, null, { 'Ocp-Apim-Subscription-Key' : key });
}

connect();
```

**Traduzir resposta de voz**

Um resultado com êxito é a criação de um ficheiro denominado "speak2.wav". O ficheiro contém a tradução da linguagem falada em "speak.wav".

[Voltar ao início](#HOLTop)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial da Tradução de Voz](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Consulte também 

[Descrição geral da Tradução de Voz](../overview.md)
[Referência da API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
