---
title: Transcrição de conversa assíncrona (versão prévia) – serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar a transcrição de conversa assíncrona usando o serviço de fala. Disponível somente para Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: f34ce66d126a47e186cd1196fb5c92d670def445
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806172"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Transcrição de conversa assíncrona (versão prévia)

Neste artigo, a transcrição de conversa assíncrona é demonstrada usando a API **RemoteConversationTranscriptionClient** . Se você configurou a transcrição de conversa para fazer a transcrição assíncrona e tiver um `conversationId`, poderá obter a transcrição associada a esse `conversationId` usando a API **RemoteConversationTranscriptionClient** .

## <a name="asynchronous-vs-real-time--asynchronous"></a>Assíncrono versus em tempo real + assíncrono

Com a transcrição assíncrona, você transmite o áudio da conversa, mas não precisa de uma transcrição retornada em tempo real. Em vez disso, depois que o áudio for enviado, use a `conversationId` de `Conversation` para consultar o status da transcrição assíncrona. Quando a transcrição assíncrona estiver pronta, você obterá uma `RemoteConversationTranscriptionResult`.

Com mais assíncrono em tempo real, você obtém a transcrição em tempo real, mas também obtém a transcrição consultando com o `conversationId` (semelhante ao cenário assíncrono).

São necessárias duas etapas para realizar a transcrição assíncrona. A primeira etapa é carregar o áudio, escolhendo somente assíncrono ou em tempo real, além de assíncrono. A segunda etapa é obter os resultados da transcrição.

## <a name="upload-the-audio"></a>Carregar o áudio

Antes que a transcrição assíncrona possa ser executada, você precisa enviar o áudio para o serviço de transcrição de conversa usando o SDK do cliente do Microsoft cognitiva Speech (versão 1.8.0 ou superior).

Este código de exemplo mostra como criar o transistor de conversa para o modo somente assíncrono. Para transmitir áudio para o transcrita, você precisará adicionar o código de streaming de áudio derivado de conversas de [transcrever em tempo real com o SDK de fala](how-to-use-conversation-transcription-service.md). Consulte a seção **limitações** do tópico para ver as plataformas com suporte e as APIs de linguagens.

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speechConfig.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// pick a conversation Id that is a GUID.
String conversationId = UUID.randomUUID().toString();

// Create a Conversation
Conversation conversation = new Conversation(speechConfig, conversationId);

// Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
// Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16Khz16Bits8channelsOfRecordedPCMAudio.wav");
// Create an audio stream format assuming the file used above is 16Khz, 16 bits and 8 channel pcm wav file
AudioStreamFormat audioStreamFormat = AudioStreamFormat.getWaveFormatPCM((long)16000, (short)16,(short)8);
// Create an input stream
AudioInputStream audioStream = AudioInputStream.createPullStream(wavfilePullStreamCallback, audioStreamFormat);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromStreamInput(audioStream));

// join a conversation
transcriber.joinConversationAsync(conversation);

// Add the event listener for the realtime events
transcriber.transcribed.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber Recognized:" + e.toString());
});

transcriber.canceled.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber canceled:" + e.toString());
    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

transcriber.sessionStopped.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber stopped:" + e.toString());

    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

// start the transcription.
Future<?> future = transcriber.startTranscribingAsync();
...
```

Se você quiser em tempo real, _além_ de assíncrona, comente e remova os comentários das linhas de código apropriadas da seguinte maneira:

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>Obter resultados da transcrição

Esta etapa Obtém os resultados da transcrição assíncrona, mas supõe que qualquer processamento em tempo real que você possa ter necessário é feito em outro lugar. Para obter mais informações, consulte [transcrever conversas em tempo real com o SDK de fala](how-to-use-conversation-transcription-service.md).

Para o código mostrado aqui, você precisa da **versão de conversa remota 1.8.0**, com suporte apenas para Java (1.8.0 ou superior) no Windows, Linux e Android (somente API nível 26 ou acima).

### <a name="obtaining-the-client-sdk"></a>Obtendo o SDK do cliente

Você pode obter a **conversa remota** editando seu arquivo pom. XML da seguinte maneira.

1. No final do arquivo, antes da marca de fechamento `</project>`, crie um elemento `repositories` com uma referência ao repositório Maven para o SDK de fala:

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. Adicione também um elemento `dependencies`, com o remoteconversation-Client-SDK 1.8.0 como uma dependência:

   ```xml
   <dependencies>
     <dependency>
       <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
       <artifactId>remote-conversation</artifactId>
       <version>1.8.0</version>
     </dependency>
   </dependencies>
   ```

3. Salvar as alterações

### <a name="sample-transcription-code"></a>Código de transcrição de exemplo

Depois de ter o `conversationId`, crie um cliente de transcrição de conversa remota **RemoteConversationTranscriptionClient** no aplicativo cliente para consultar o status da transcrição assíncrona. Use o método **getTranscriptionOperation** no **RemoteConversationTranscriptionClient** para obter um objeto [PollerFlux](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java) . O objeto PollerFlux terá informações sobre o status da operação remota **RemoteConversationTranscriptionOperation** e o resultado final **RemoteConversationTranscriptionResult**. Depois que a operação for concluída, obtenha **RemoteConversationTranscriptionResult** chamando **GetFinalResult** em um [SyncPoller](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java). Nesse código, simplesmente imprimemos o conteúdo do resultado na saída do sistema.

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Get the PollerFlux for the remote operation
PollerFlux<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> remoteTranscriptionOperation = client.getTranscriptionOperation(conversationId);

// Subscribe to PollerFlux to get the remote operation status
remoteTranscriptionOperation.subscribe(
        pollResponse -> {
            System.out.println("Poll response status : " + pollResponse.getStatus());
            System.out.println("Poll response status : " + pollResponse.getValue().getServiceStatus());
        }
);

// Obtain the blocking operation using getSyncPoller
SyncPoller<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> blockingOperation =  remoteTranscriptionOperation.getSyncPoller();

// Wait for the operation to finish
blockingOperation.waitForCompletion();

// Get the final result response
RemoteConversationTranscriptionResult resultResponse = blockingOperation.getFinalResult();

// Print the result
if(resultResponse != null) {
    if(resultResponse.getConversationTranscriptionResults() != null) {
        for (int i = 0; i < resultResponse.getConversationTranscriptionResults().size(); i++) {
            ConversationTranscriptionResult result = resultResponse.getConversationTranscriptionResults().get(i);
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult.name()));
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult));
            System.out.println(result.getOffset());
            System.out.println(result.getDuration());
            System.out.println(result.getUserId());
            System.out.println(result.getReason());
            System.out.println(result.getResultId());
            System.out.println(result.getText());
            System.out.println(result.toString());
        }
    }
}

System.out.println("Operation finished");
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore nossos exemplos no GitHub](https://aka.ms/csspeech/samples)
