---
title: Transcrição de Conversa ção assíncrona (Pré-visualização) - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Aprenda a usar a transcrição de conversação assíncrona utilizando o serviço de Fala. Disponível apenas para Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: c1f0110c83eb42aaedbd36736946ae3faff58699
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80366616"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Transcrição de Conversação Assíncrona (Pré-visualização)

Neste artigo, a Transcrição de Conversas Assíncronas é demonstrada através da **API RemoteConversationTranscriptionClient.** Se tiver configurado a Transcrição de Conversação para fazer transcrição `conversationId`assíncrona e tiver uma, pode obter a transcrição associada a essa `conversationId` utilização da **API RemoteConversationTranscriptionClient.**

## <a name="asynchronous-vs-real-time--asynchronous"></a>Assíncrono vs. tempo real + assíncrono

Com transcrição assíncrona, transmite o áudio da conversa, mas não precisa de uma transcrição devolvida em tempo real. Em vez disso, após o `conversationId` `Conversation` envio do áudio, utilize o de saque para o estado da transcrição assíncrona. Quando a transcrição assíncrona estiver pronta, terá `RemoteConversationTranscriptionResult`um.

Com tempo real mais assíncrono, obtém-se a transcrição em tempo real, mas também `conversationId` obtém a transcrição consultando-se com o (semelhante ao cenário assíncrono).

São necessários dois passos para realizar a transcrição assíncrona. O primeiro passo é carregar o áudio, escolhendo apenas assíncrono ou em tempo real mais assíncrono. O segundo passo é obter os resultados da transcrição.

## <a name="upload-the-audio"></a>Faça upload do áudio

Antes de ser realizada uma transcrição assíncrona, é necessário enviar o áudio para o Serviço de Transcrição de Conversas utilizando o cliente da Microsoft Cognitive Speech SDK (versão 1.8.0 ou superior).

Este código de exemplo mostra como criar transcritor de conversação para o modo apenas assíncrono. Para transmitir áudio ao transcritor, terá de adicionar código de streaming de áudio derivado de [conversas transcritoras em tempo real com o Speech SDK](how-to-use-conversation-transcription-service.md). Consulte a secção **limitações** desse tópico para ver as APIs de plataformas e idiomas suportadas.

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
Future<Conversation> conversationFuture = Conversation.createConversationAsync(speechConfig, conversationId);
Conversation conversation = conversationFuture.get();

// Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
// Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16kHz16Bits8channelsOfRecordedPCMAudio.wav");
// Create an audio stream format assuming the file used above is 16kHz, 16 bits and 8 channel pcm wav file
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

Se quiser em tempo real _mais_ assíncronos, comente e não comente as linhas de código apropriadas da seguinte forma:

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>Obtenha resultados de transcrição

Este passo obtém os resultados da transcrição assíncrona, mas assume que qualquer processamento em tempo real que possa ter exigido é feito em outro lugar. Para mais informações, consulte [conversas transcritoras em tempo real com o SDK do Discurso](how-to-use-conversation-transcription-service.md).

Para o código aqui mostrado, precisa da **versão 1.8.0**de conversa remota, suportada apenas para Java (1.8.0 ou superior) no Windows, Linux e Android (apenas nível API 26 ou superior).

### <a name="obtaining-the-client-sdk"></a>Obtenção do SDK cliente

Pode obter **conversação remota** editando o seu ficheiro pom.xml da seguinte forma.

1. No final do ficheiro, antes `</project>`da etiqueta `repositories` de fecho, crie um elemento com referência ao repositório Maven para o SDK da Fala:

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. Adicione também `dependencies` um elemento, com o cliente-sdk 1.8.0 como dependência:

   ```xml
   <dependencies>
     <dependency>
       <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
       <artifactId>remote-conversation</artifactId>
       <version>1.8.0</version>
     </dependency>
   </dependencies>
   ```

3. Guarde as alterações

### <a name="sample-transcription-code"></a>Código de transcrição da amostra

Depois de `conversationId`ter o , crie um cliente de transcrição de conversas **remotas RemoteConversationTranscriptionClient** na aplicação do cliente para consultar o estado da transcrição assíncrona. Utilize o método **getTranscriptionOperation** no **RemoteConversationTranscriptionClient** para obter um objeto [PollerFlux.](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java) O objeto PollerFlux terá informações sobre o estado de operação remota **RemoteConversationTranscriptionOperation** e o resultado final **RemoteConversationTranscriptionResult**. Uma vez terminada a operação, obtenha **RemoteConversationTranscriptionResult,** ligando para **getFinalResult** num [SyncPoller](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java). Neste código, simplesmente imprimimos o conteúdo do resultado à saída do sistema.

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
> [Explore as nossas amostras no GitHub](https://aka.ms/csspeech/samples)
