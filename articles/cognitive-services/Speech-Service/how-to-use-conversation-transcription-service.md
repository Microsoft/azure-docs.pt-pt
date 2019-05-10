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
ms.date: 05/06/2019
ms.author: jhakulin
ms.openlocfilehash: e9de4faf18c54f7c7582ef5a8ab0648629d4f48e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190157"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Transcrição de vários participantes conversas com o SDK de voz

O SDK de voz **ConversationTranscriber** API permite-lhe a transcrição de reuniões/conversas com a capacidade de adicionar, remover e identificar os participantes por transmissão em fluxo de áudio para o serviços de voz com `PullStream` ou `PushStream`.

## <a name="limitations"></a>Limitações

* Transcriber conversa é suportada para o C++, C#e o Java no Android, Linux e Windows.
* O DevKit ROOBO é o ambiente de hardware suportado para a criação de conversações à medida que fornece a matriz de microfone multi circular que pode ser utilizado de forma eficiente pelo serviço de transcrição da conversação para a identificação de orador. [Para obter mais informações, consulte o SDK de dispositivos de voz](speech-devices-sdk.md).
* Suporte do SDK de voz está limitado a utilizar de solicitação de áudio e emitir fluxos de modo com oito canais de áudio PCM.

## <a name="prerequisites"></a>Pré-requisitos

* [Saiba como utilizar a voz para texto com o SDK de voz.](quickstart-csharp-dotnet-windows.md)
* [Obtenha a sua subscrição de avaliação de conversão de voz.](https://azure.microsoft.com/try/cognitive-services/)

## <a name="create-voice-signatures-for-participants"></a>Criar assinaturas de voz para os participantes

A primeira etapa é criar assinaturas de voz para os participantes da conversa. A criação de assinaturas de voz é necessário para a identificação de orador eficiente.
No exemplo a seguir, a Microsoft irá [utilizar a API REST para obter a assinatura de voz.](https://aka.ms/cts/signaturegenservice)

O exemplo abaixo mostra duas formas diferentes de criar assinaturas de voz:
```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://{region}.signature.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature can be extracted from the jsonData
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature can be extracted from the jsonData
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
    private static string endpoint = "YourOwnEndpoint";

    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region.
        // Replace with your own endpoint and subscription key.
        var config = SpeechConfig.FromEndpoint(new Uri(endpoint), "YourSubScriptionKey");
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
                        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}, SpeakerID={e.Result.SpeakerId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                // Sets a conversation Id.
                transcriber.ConversationId = "AConversationFromTeams";

                // Add participants to the conversation.
                // Create data for voice signatures using REST API described in the earlier section in this document.
                // How to create voice signatureA, signatureB & signatureC variables, please check the SDK API samples.

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
