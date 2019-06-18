---
title: Transcrição de vários participantes conversas com o SDK de voz - serviços de voz
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar a transcrição de conversação com o SDK de voz. Disponível para C++, C#e Java.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jhakulin
ms.openlocfilehash: f0838d345abfcfdf69ca6ea44d3206c23010b457
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073094"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Transcrição de vários participantes conversas com o SDK de voz

O SDK de voz **ConversationTranscriber** API permite-lhe a transcrição de reuniões/conversas com a capacidade de adicionar, remover e identificar os participantes por transmissão em fluxo de áudio para o serviços de voz com `PullStream` ou `PushStream`.

## <a name="limitations"></a>Limitações

* Transcriber conversa é suportada para o C++, C#e o Java no Android, Linux e Windows.
* O DevKit ROOBO é o ambiente de hardware suportado para a criação de transcrições de conversa à medida que fornece circular matriz de microfone multi que pode ser utilizado com eficiência para a identificação de orador. [Para obter mais informações, consulte o SDK de dispositivos de voz](speech-devices-sdk.md).
* Suporte do SDK de voz para transcrição da conversação está limitado a utilizar de solicitação de áudio e fluxos de modo com oito canais de áudio PCM 16 kHz de 16 bits de push.
* Transcrição da conversação está atualmente disponível em idiomas de "en-US" e "zh-CN" nas seguintes regiões: centralus e Sudeste asiático.

## <a name="prerequisites"></a>Pré-requisitos

* [Saiba como utilizar a voz para texto com o SDK de voz.](quickstart-csharp-dotnet-windows.md)
* [Obtenha a sua subscrição de avaliação de conversão de voz.](https://azure.microsoft.com/try/cognitive-services/)
* 1\.5.1 na versão do SDK de voz ou posterior é necessária.

## <a name="create-voice-signatures-for-participants"></a>Criar assinaturas de voz para os participantes

A primeira etapa é criar assinaturas de voz para os participantes da conversa. A criação de assinaturas de voz é necessário para a identificação de orador eficiente.

### <a name="requirements-for-input-wave-file"></a>Requisitos para o ficheiro de entrada wave

* O ficheiro de onda de áudio de entrada para a criação de assinaturas de voz deve estar em exemplos de 16 bits, taxa de amostragem 16 kHz e um formato de canal único (Mono).
* O comprimento recomendado para cada amostra de áudio é entre 30 segundos e dois minutos.

O exemplo seguinte mostra duas formas diferentes de criar a assinatura de voz por [com a API REST](https://aka.ms/cts/signaturegenservice) de C#:

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribing-conversations"></a>Fotografar conversas

A transcrição de conversas com vários participantes, criar o `ConversationTranscriber` objeto que está associado a `AudioConfig` objeto criado para a sessão de conversação e, em seguida, fluxo de áudio usando `PullAudioInputStream` ou `PushAudioInputStream`.

Vamos supor que tem uma classe de ConversationTranscriber chamada `MyConversationTranscriber`. Seu código pode ter este aspeto:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Conversation;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region.
        // Replace with your own subscription key and region.
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file.
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            // Creates a conversation transcriber using audio stream input.
            using (var transcriber = new ConversationTranscriber(config, audioInput))
            {
                // Subscribes to events.
                transcriber.Recognizing += (s, e) =>
                {
                    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                };

                transcriber.Recognized += (s, e) =>
                {
                    if (e.Result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                transcriber.Canceled += (s, e) =>
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

                transcriber.SessionStarted += (s, e) =>
                {
                    Console.WriteLine("\nSession started event.");
                };

                transcriber.SessionStopped += (s, e) =>
                {
                    Console.WriteLine("\nSession stopped event.");
                    Console.WriteLine("\nStop recognition.");
                    stopTranscription.TrySetResult(0);
                };

                // Sets a conversation Id.
                transcriber.ConversationId = "AConversationFromTeams";

                // Add participants to the conversation.
                // Create voice signatures using REST API described in the earlier section in this document. 
                // Voice signature needs to be in the following format:
                // { "Version": <Numeric value>, "Tag": "string", "Data": "string" }

                var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                transcriber.AddParticipant(speakerA);
                transcriber.AddParticipant(speakerB);
                transcriber.AddParticipant(speakerC);

                // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                await transcriber.StartTranscribingAsync().ConfigureAwait(false);

                // Waits for completion.
                // Use Task.WaitAny to keep the task rooted.
                Task.WaitAny(new[] { stopTranscription.Task });

                // Stop transcribing the conversation.
                await transcriber.StopTranscribingAsync().ConfigureAwait(false);

                // Ends the conversation.
                await transcriber.EndConversationAsync().ConfigureAwait(false);
            }
        }
    }
}
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explore os nossos exemplos no GitHub](https://aka.ms/csspeech/samples)
