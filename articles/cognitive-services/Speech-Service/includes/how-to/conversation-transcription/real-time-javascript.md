---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 10/20/2020
ms.author: trbye
ms.openlocfilehash: 502729e4c90a6d6ffa424ea7f379e2b426b3e3e0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102444304"
---
## <a name="install-the-speech-sdk"></a>Instale o SDK de discurso

Antes de poder fazer qualquer coisa, terá de instalar o <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">SDK </a>de voz para JavaScript . Dependendo da sua plataforma, utilize as seguintes instruções:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Navegador Web </a>

## <a name="create-voice-signatures"></a>Criar assinaturas de voz

O primeiro passo é criar assinaturas de voz para os participantes da conversa para que possam ser identificados como oradores únicos. O ficheiro áudio de entrada `.wav` para a criação de assinaturas de voz deve ser de 16 bits, 16 kHz de taxa de amostra e formato de canal único (mono). O comprimento recomendado para cada amostra de áudio é entre 30 segundos e dois minutos. O `.wav` ficheiro deve ser uma amostra da voz de uma pessoa para que seja criado um perfil **de** voz único.

O exemplo a seguir mostra como criar uma assinatura de voz [utilizando a API REST](https://aka.ms/cts/signaturegenservice) em JavaScript. Note que precisa de substituir informações reais pelo seu `subscriptionKey` , e o caminho para um ficheiro de `region` `.wav` amostra.

```javascript
const fs = require('fs');
const axios = require('axios');
const formData = require('form-data');
 
const subscriptionKey = 'your-subscription-key';
const region = 'your-region';
 
async function createProfile() {
    let form = new formData();
    form.append('file', fs.createReadStream('path-to-voice-sample.wav'));
    let headers = form.getHeaders();
    headers['Ocp-Apim-Subscription-Key'] = subscriptionKey;
 
    let url = `https://signature.${region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData`;
    let response = await axios.post(url, form, { headers: headers });
    
    // get signature from response, serialize to json string
    return JSON.stringify(response.data.Signature);
}
 
async function main() {
    // use this voiceSignature string with conversation transcription calls below
    let voiceSignatureString = await createProfile();
    console.log(voiceSignatureString);
}
main();
```

Executar este script devolve uma cadeia de assinatura de voz na variável `voiceSignatureString` . Executar a função duas vezes para que tenha duas cordas para usar como entrada para as variáveis `voiceSignatureStringUser1` e `voiceSignatureStringUser2` abaixo.

> [!NOTE]
> As assinaturas de voz **só** podem ser criadas utilizando a API REST.

## <a name="transcribe-conversations"></a>Transcrever conversas

O seguinte código de amostra demonstra como transcrever conversas em tempo real para dois altifalantes. Pressupõe que já criou cordas de assinatura de voz para cada altifalante, como mostrado acima. Substitua informações reais para `subscriptionKey` , e o caminho para o áudio que pretende `region` `filepath` transcrever.

Este código de amostra faz o seguinte:

* Cria um fluxo de impulso para usar para transcrição, e escreve o ficheiro da amostra `.wav` para ele.
* Cria uma `Conversation` utilização `createConversationAsync()` .
* Cria um `ConversationTranscriber` uso do construtor.
* Acrescenta os participantes à conversa. As cordas `voiceSignatureStringUser1` e devem vir como saída dos `voiceSignatureStringUser2` degraus acima.
* Regista-se a eventos e inicia a transcrição.

```javascript
(function() {
    "use strict";
    var sdk = require("microsoft-cognitiveservices-speech-sdk");
    var fs = require("fs");
    
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";
    var filepath = "audio-file-to-transcribe.wav"; // 8-channel audio
    
    // create the push stream and write file to it
    var pushStream = sdk.AudioInputStream.createPushStream();
    fs.createReadStream(filepath).on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
    
    var speechTranslationConfig = sdk.SpeechTranslationConfig.fromSubscription(subscriptionKey, region);
    var audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    speechTranslationConfig.speechRecognitionLanguage = "en-US";
    
    // create conversation and transcriber
    var conversation = sdk.Conversation.createConversationAsync(speechTranslationConfig, "myConversation");
    var transcriber = new sdk.ConversationTranscriber(audioConfig);
    
    // attach the transcriber to the conversation
    transcriber.joinConversationAsync(conversation,
    function () {
        // add first participant using voiceSignature created in enrollment step
        var user1 = sdk.Participant.From("user1@example.com", "en-us", voiceSignatureStringUser1);
        conversation.addParticipantAsync(user1,
        function () {
            // add second participant using voiceSignature created in enrollment step
            var user2 = sdk.Participant.From("user2@example.com", "en-us", voiceSignatureStringUser2);
            conversation.addParticipantAsync(user2,
            function () {
                transcriber.sessionStarted = function(s, e) {
                console.log("(sessionStarted)");
                };
                transcriber.sessionStopped = function(s, e) {
                console.log("(sessionStopped)");
                };
                transcriber.canceled = function(s, e) {
                console.log("(canceled)");
                };
                transcriber.transcribed = function(s, e) {
                console.log("(transcribed) text: " + e.result.text);
                console.log("(transcribed) speakerId: " + e.result.speakerId);
                };
    
                // begin conversation transcription
                transcriber.startTranscribingAsync(
                function () { },
                function (err) {
                    console.trace("err - starting transcription: " + err);
                });
        },
        function (err) {
            console.trace("err - adding user1: " + err);
        });
    },
    function (err) {
        console.trace("err - adding user2: " + err);
    });
    },
    function (err) {
    console.trace("err - " + err);
    });
}()); 
```