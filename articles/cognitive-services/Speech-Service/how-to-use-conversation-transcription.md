---
title: Transcrição de conversa em tempo real (versão prévia) – serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar a transcrição de conversa em tempo real com o SDK de fala. Disponível para C++, C#e Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.openlocfilehash: 64a9e11cec7164fb4421dd018238de9f0670382b
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263732"
---
# <a name="real-time-conversation-transcription-preview"></a>Transcrição de conversa em tempo real (visualização)

A API **ConversationTranscriber** do SDK de fala permite transcrever reuniões e outras conversas com a capacidade de adicionar, remover e identificar vários participantes transmitindo o áudio para o serviço de fala usando `PullStream` ou `PushStream`. Este tópico exige que você saiba como usar a conversão de fala em texto com o SDK de fala (versão 1.8.0 ou posterior). Para obter mais informações, consulte [o que são os serviços de fala](overview.md).

## <a name="limitations"></a>Limitações

- A API ConversationTranscriber tem suporte para C++o C#, o e o Java no Windows, Linux e Android.
- Atualmente disponível nos idiomas "en-US" e "zh-CN" nas seguintes regiões: _centralus_ e _eastasia_.
- Requer uma matriz de vários microfones circulares de 7 Mic com um fluxo de referência de reprodução. A matriz de microfone deve atender à [nossa especificação](https://aka.ms/sdsdk-microphone).
- O [SDK dos dispositivos de fala](speech-devices-sdk.md) fornece dispositivos adequados e um aplicativo de exemplo que demonstra a transcrição da conversa.

## <a name="optional-sample-code-resources"></a>Recursos de código de exemplo opcionais

O SDK do dispositivo de fala fornece código de exemplo em Java para captura de áudio em tempo real usando 8 canais.

- [Código de exemplo do dispositivo ROOBO](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/Conversation.java)
- [Código de exemplo do kit de desenvolvimento do Azure Kinect](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>Pré-requisitos

Uma assinatura do serviço de fala. Você pode [obter uma assinatura de avaliação de fala](https://azure.microsoft.com/try/cognitive-services/) se não tiver uma.

## <a name="create-voice-signatures"></a>Criar assinaturas de voz

A primeira etapa é criar assinaturas de voz para os participantes da conversa para uma identificação de alto-falante eficiente.

### <a name="audio-input-requirements"></a>Requisitos de entrada de áudio

- O arquivo wave de entrada de áudio para criar assinaturas de voz deve estar em amostras de 16 bits, taxa de amostra de 16 kHz e um único formato de canal (mono).
- O comprimento recomendado para cada amostra de áudio é entre trinta e dois minutos.

### <a name="sample-code"></a>Código de exemplo

O exemplo a seguir mostra duas maneiras diferentes de criar a assinatura de voz [usando a API REST](https://aka.ms/cts/signaturegenservice) no C#. Observe que você precisará substituir as informações reais de "YourSubscriptionKey", seu nome de arquivo wave por "speakerVoice. wav" e sua região por `{region}` e "YourServiceRegion" (_centralus_ ou _eastasia_).

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribe-conversations"></a>Transcrever conversas

O código de exemplo a seguir demonstra como transcrever conversas em tempo real para três alto-falantes. Ele pressupõe que você já criou assinaturas de voz para cada palestrante, conforme mostrado acima. Substitua as informações reais para "YourSubscriptionKey" e "YourServiceRegion" ao criar o objeto SpeechConfig.

Os destaques do código de exemplo incluem:

- Criando um objeto `Conversation` do objeto `SpeechConfig` usando um identificador de reunião gerado usando `Guid.NewGuid()`
- Criando um objeto `ConversationTranscriber` e ingressar na conversa com `JoinConversationAsync()` para iniciar a transcrição
- Registrando os eventos de interesse
- Adicionando ou removendo participantes da conversa usando o objeto de conversa
- Transmitindo o áudio
- No SDK de fala versão 1.9.0 e em diante, `int` e `string` tipos de valor têm suporte no campo versão da assinatura de voz.

A transcrição e o identificador do palestrante retornam nos eventos registrados.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Transcription;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region
        // Replace with your own subscription key and region
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            var meetingId = Guid.NewGuid().ToString();
            using (var conversation = await Conversation.CreateConversationAsync(config, meetingId).ConfigureAwait(false))
            {
                // Create a conversation transcriber using audio stream input
                using (var conversationTranscriber = new ConversationTranscriber    (audioInput))
                {
                    await conversationTranscriber.JoinConversationAsync(conversation);

                    // Subscribe to events
                    conversationTranscriber.Transcribing += (s, e) =>
                    {
                            Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text}");
                    };

                    conversationTranscriber.Transcribed += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    conversationTranscriber.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");

                        if (e.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                            stopTranscription.TrySetResult(0);
                        }
                    };

                    conversationTranscriber.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\nSession started event.");
                    };

                    conversationTranscriber.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\nSession stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopTranscription.TrySetResult(0);
                    };

                    // Add participants to the conversation.
                    // Create voice signatures using REST API described in the earlier section in this document.
                    // Voice signature needs to be in the following format:
                    // { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }

                    var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                    var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                    var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                    await conversation.AddParticipantAsync(speakerA);
                    await conversation.AddParticipantAsync(speakerB);
                    await conversation.AddParticipantAsync(speakerC);

                    // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                    await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopTranscription.Task });

                    // Stop transcribing the conversation.
                    await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
                 }
            }
       }
    }
}
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Transcrição de conversa assíncrona](how-to-async-conversation-transcription.md)
