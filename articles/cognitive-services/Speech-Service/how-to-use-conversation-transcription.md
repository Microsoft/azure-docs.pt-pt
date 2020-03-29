---
title: Transcrição de conversação em tempo real (Pré-visualização) - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Aprenda a usar a Transcrição de Conversação em tempo real com o SDK de Discurso. Disponível para C++, C#, e Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.openlocfilehash: 64a9e11cec7164fb4421dd018238de9f0670382b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76263732"
---
# <a name="real-time-conversation-transcription-preview"></a>Transcrição de conversação em tempo real (Pré-visualização)

A **API ConversationTranscriber** do Speech SDK permite-lhe transcrever reuniões e outras conversas com a capacidade de `PullStream` adicionar, remover e identificar múltiplos participantes através do streaming de áudio para o serviço de Fala usando ou `PushStream`. Este tópico requer que saiba como usar o Discurso-a-texto com o SDK do Discurso (versão 1.8.0 ou mais tarde). Para mais informações, consulte [o que são os serviços de Fala.](overview.md)

## <a name="limitations"></a>Limitações

- O ConversationTranscriber API é suportado para C++, C#e Java no Windows, Linux e Android.
- Atualmente disponível em línguas "en-US" e "zh-CN" nas seguintes regiões: _centrale_ _eastasia_.
- Requer uma matriz circular de microfone circular de 7 micrômicos com um fluxo de referência de reprodução. A matriz do microfone deve cumprir as [nossas especificações.](https://aka.ms/sdsdk-microphone)
- O [SDK dispositivos de fala](speech-devices-sdk.md) fornece dispositivos adequados e uma aplicação de amostra que demonstra a Transcrição de Conversas.

## <a name="optional-sample-code-resources"></a>Recursos opcionais do código da amostra

O Dispositivo de Fala SDK fornece código de amostra em Java para captura de áudio em tempo real usando 8 canais.

- [Código de amostra do dispositivo ROOBO](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/Conversation.java)
- [Código de amostra azure Kinect Dev Kit](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>Pré-requisitos

Uma assinatura do serviço de fala. Pode [sacá-lo se](https://azure.microsoft.com/try/cognitive-services/) não tiver uma.

## <a name="create-voice-signatures"></a>Criar assinaturas de voz

O primeiro passo é criar assinaturas de voz para os participantes da conversa para uma identificação eficiente dos altifalantes.

### <a name="audio-input-requirements"></a>Requisitos de entrada de áudio

- O ficheiro de onda de áudio de entrada para a criação de assinaturas de voz deve estar em amostras de 16 bits, uma taxa de amostra de 16 kHz e um único formato de canal (mono).
- O comprimento recomendado para cada amostra de áudio é entre 30 segundos e dois minutos.

### <a name="sample-code"></a>Código de exemplo

O exemplo seguinte mostra duas formas diferentes de criar a assinatura de voz [utilizando a API REST](https://aka.ms/cts/signaturegenservice) em C#. Tenha em anote que terá de substituir informações reais por "YourSubscriptionKey", o seu `{region}` nome de ficheiro de onda para "speakerVoice.wav", e a sua região para e "YourServiceRegion"_(centralou_ leste _da Ásia)._

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

## <a name="transcribe-conversations"></a>Conversas transcritas

O seguinte código de amostra demonstra como transcrever conversas em tempo real para três oradores. Assume que já criou assinaturas de voz para cada orador, como mostrado acima. Substitua informações reais por "YourSubscriptionKey" e "YourServiceRegion" ao criar o objeto SpeechConfig.

Os destaques do código da amostra incluem:

- Criar `Conversation` um objeto `SpeechConfig` a partir do objeto usando um identificador de reunião gerado usando`Guid.NewGuid()`
- Criar `ConversationTranscriber` um objeto e `JoinConversationAsync()` juntar a conversa com para começar a transcrição
- Registar os acontecimentos de interesse
- Adicionar ou remover os participantes à conversa utilizando o objeto Conversador
- Streaming do áudio
- Na versão 1.9.0 do Speech SDK e em frente tanto `int` como `string` os tipos de valor são suportados no campo da versão de assinatura de voz.

A transcrição e o identificador do orador voltam nos eventos registados.

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
> [Transcrição de Conversas Assíncrona](how-to-async-conversation-transcription.md)
