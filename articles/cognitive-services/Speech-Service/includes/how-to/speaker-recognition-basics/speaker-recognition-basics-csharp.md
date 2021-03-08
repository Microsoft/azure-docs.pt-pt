---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 35fc646eed802dbff80c82cadfdbba5f96397481
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444198"
---
Neste arranque rápido, você aprende padrões básicos de design para reconhecimento de alto-falante usando o SDK do discurso, incluindo:

* Verificação dependente de texto e independente de texto
* Identificação de altifalantes para identificar uma amostra de voz entre um grupo de vozes
* Eliminação de perfis de voz

Para uma visão de alto nível dos conceitos de reconhecimento de voz, consulte o artigo [de visão geral.](../../../speaker-recognition-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem uma conta Azure e subscrição do serviço de fala. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço Desemação](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Atualmente, o Reconhecimento de Oradores *só* é suportado nos recursos do Discurso Azure criados na `westus` região.

## <a name="install-the-speech-sdk"></a>Instale o SDK de discurso

Antes de poder fazer qualquer coisa, terá de instalar o SDK do Discurso. Dependendo da sua plataforma, utilize as seguintes instruções:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unidade </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin </a>

## <a name="import-dependencies"></a>Dependências de importação

Para executar os exemplos neste artigo, inclua as seguintes `using` declarações no topo do seu script.

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>Criar uma configuração de discurso

Para ligar para o serviço de discurso usando o SDK de discurso, precisa de criar um [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) . Neste exemplo, cria-se uma [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) chave de subscrição e uma região. Também cria um código básico de placa de caldeira para usar para o resto deste artigo, que modifica para diferentes personalizações.

Note-se que a região está definida para `westus` , uma vez que é a única região apoiada para o serviço.

```csharp
public class Program 
{
    static async Task Main(string[] args)
    {
        // replace with your own subscription key 
        string subscriptionKey = "YourSubscriptionKey";
        string region = "westus";
        var config = SpeechConfig.FromSubscription(subscriptionKey, region);
    }
}
```

## <a name="text-dependent-verification"></a>Verificação dependente de texto

A Verificação do Orador é o ato de confirmar que um orador corresponde a uma voz conhecida ou **inscrita.** O primeiro passo é **inscrever** um perfil de voz, para que o serviço tenha algo para comparar futuras amostras de voz com. Neste exemplo, você inscreve o perfil usando uma estratégia **dependente de texto,** que requer uma frase-passe específica para usar tanto para a inscrição como para a verificação. Consulte os [documentos de referência](/rest/api/speakerrecognition/) para obter uma lista de frases-passe apoiadas.

Comece por criar a seguinte função na sua `Program` classe para inscrever um perfil de voz.

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextDependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsCount > 0)
            {
                Console.WriteLine("Speak the passphrase, \"My voice is my passport, verify me.\"");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollments needed: {result.RemainingEnrollmentsCount}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

Nesta função, `await client.CreateProfileAsync()` é o que realmente cria o novo perfil de voz. Depois de criado, especifica como irá introduzir amostras de áudio, utilizando `AudioConfig.FromDefaultMicrophoneInput()` neste exemplo para capturar áudio a partir do seu dispositivo de entrada padrão. Em seguida, você inscreve amostras de áudio em um `while` loop que rastreia o número de amostras restantes, e necessárias, para a inscrição. Em cada iteração, `client.EnrollProfileAsync(profile, audioInput)` irá instruí-lo a falar a frase-passe no microfone e adicionar a amostra ao perfil de voz.

Após a conclusão da inscrição, ligue `await SpeakerVerify(config, profile, profileMapping)` para verificar contra o perfil que acabou de criar. Adicione outra função para definir `SpeakerVerify` .

```csharp
public static async Task SpeakerVerify(SpeechConfig config, VoiceProfile profile, Dictionary<string, string> profileMapping)
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerVerificationModel.FromProfile(profile);

    Console.WriteLine("Speak the passphrase to verify: \"My voice is my passport, please verify me.\"");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"Verified voice profile for speaker {profileMapping[result.ProfileId]}, score is {result.Score}");
}
```

Nesta função, passa-se o `VoiceProfile` objeto que acabou de criar para rubricar um modelo para verificar contra. Em seguida, `await speakerRecognizer.RecognizeOnceAsync(model)` pede-lhe que volte a falar a frase-passe, mas desta vez irá validá-la contra o seu perfil de voz e devolver uma pontuação de semelhança que varia entre 0,0 e 1.0. O `result` objeto também regressa `Accept` `Reject` ou, com base no facto de a frase-passe corresponder ou não.

Em seguida, modifique a sua `Main()` função para chamar as novas funções que criou. Além disso, note que cria um `Dictionary<string, string>` para passar por referência através das suas chamadas de função. A razão para isso é que o serviço não permite armazenar um nome legível pelo homem com um criado `VoiceProfile` , e apenas armazena um número de ID para fins de privacidade. Na `VerificationEnroll` função, adicione a este dicionário uma entrada com o iD recém-criado, juntamente com um nome de texto. Em cenários de desenvolvimento de aplicações onde você precisa apresentar um nome legível pelo homem, **você deve armazenar este mapeamento em algum lugar, o serviço não pode armazená-lo**.

```csharp
static async Task Main(string[] args)
{
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    await VerificationEnroll(config, profileMapping);

    Console.ReadLine();
}
```

Faz o guião, e és solicitado a dizeres a frase A minha voz é o *meu passaporte, verifica-me* três vezes para a inscrição, e um tempo adicional para a verificação. O resultado devolvido é a pontuação de semelhança, que pode usar para criar os seus próprios limiares personalizados para verificação.

```shell
Enrolling profile id 87-2cef-4dff-995b-dcefb64e203f.
Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 2

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 1

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 0

Speak the passphrase to verify: "My voice is my passport, verify me."
Verified voice profile for speaker Your Name, score is 0.915581
```

## <a name="text-independent-verification"></a>Verificação independente de texto

Em contraste com a verificação **dependente de texto,** verificação **independente de texto:**

* Não requer uma certa frase-passe para ser falada, qualquer coisa pode ser falada
* Não requer três amostras de áudio, mas *requer* 20 segundos de áudio total

Faça algumas alterações simples na sua `VerificationEnroll` função para mudar para verificação **independente de texto.** Primeiro, muda-se o tipo de verificação para `VoiceProfileType.TextIndependentVerification` . Em seguida, altere o `while` loop para track , que continuará a `result.RemainingEnrollmentsSpeechLength` pedir-lhe para falar até que 20 segundos de áudio tenham sido capturados.

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
            {
                Console.WriteLine("Continue speaking to add to the profile enrollment sample.");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

Volte a executar o programa e fale qualquer coisa durante a fase de verificação, uma vez que não é necessária uma frase-passe. Mais uma vez, a pontuação de semelhança é devolvida.

```shell
Enrolling profile id 4tt87d4-f2d3-44ae-b5b4-f1a8d4036ee9.
Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:15.3200000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:09.8100008

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:05.1900000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00.8700000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00

Speak the passphrase to verify: "My voice is my passport, please verify me."
Verified voice profile for speaker Your Name, score is 0.849409
```

## <a name="speaker-identification"></a>Identificação de orador

A identificação do altifalante é usada para determinar **quem** está a falar de um determinado grupo de vozes inscritas. O processo é muito semelhante à **verificação independente de texto,** com a principal diferença a ser capaz de verificar contra vários perfis de voz ao mesmo tempo, em vez de verificar com um único perfil.

Crie uma função `IdentificationEnroll` para inscrever vários perfis de voz. O processo de inscrição para cada perfil é o mesmo que o processo de inscrição para **verificação independente de texto,** e requer 20 segundos de áudio para cada perfil. Esta função aceita uma lista de cordas `profileNames` e criará um novo perfil de voz para cada nome da lista. A função retorna uma lista de `VoiceProfile` objetos, que utiliza na função seguinte para identificar um altifalante.

```csharp
public static async Task<List<VoiceProfile>> IdentificationEnroll(SpeechConfig config, List<string> profileNames, Dictionary<string, string> profileMapping)
{
    List<VoiceProfile> voiceProfiles = new List<VoiceProfile>();
    using (var client = new VoiceProfileClient(config))
    {
        foreach (string name in profileNames)
        {
            using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
            {
                var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentIdentification, "en-us");
                Console.WriteLine($"Creating voice profile for {name}.");
                profileMapping.Add(profile.Id, name);

                VoiceProfileEnrollmentResult result = null;
                while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
                {
                    Console.WriteLine($"Continue speaking to add to the profile enrollment sample for {name}.");
                    result = await client.EnrollProfileAsync(profile, audioInput);
                    Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                    Console.WriteLine("");
                }
                voiceProfiles.Add(profile);
            }
        }
    }
    return voiceProfiles;
}
```

Crie a seguinte `SpeakerIdentification` função para apresentar um pedido de identificação. A principal diferença nesta função em comparação com um pedido de verificação de **altifalantes** é a utilização de `SpeakerIdentificationModel.FromProfiles()` , que aceita uma lista de `VoiceProfile` objetos. 

```csharp
public static async Task SpeakerIdentification(SpeechConfig config, List<VoiceProfile> voiceProfiles, Dictionary<string, string> profileMapping) 
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerIdentificationModel.FromProfiles(voiceProfiles);

    Console.WriteLine("Speak some text to identify who it is from your list of enrolled speakers.");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"The most similar voice profile is {profileMapping[result.ProfileId]} with similarity score {result.Score}");
}
```

Mude a sua `Main()` função para o seguinte. Cria-se uma lista de `profileNames` cordas, que passa para a sua `IdentificationEnroll()` função. Isto irá levá-lo a criar um novo perfil de voz para cada nome nesta lista, para que possa adicionar mais nomes para criar perfis adicionais para amigos ou colegas.

```csharp
static async Task Main(string[] args)
{
    // replace with your own subscription key 
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    var profileNames = new List<string>() { "Your name", "A friend's name" };
    
    var enrolledProfiles = await IdentificationEnroll(config, profileNames, profileMapping);
    await SpeakerIdentification(config, enrolledProfiles, profileMapping);

    foreach (var profile in enrolledProfiles)
    {
        profile.Dispose();
    }
    Console.ReadLine();
}
```

Faça o guião e é solicitado a falar para inscrever amostras de voz para o primeiro perfil. Após a inscrição estar concluída, é solicitado que repita este processo para cada nome da lista `profileNames` . Após cada inscrição terminar, é solicitado que **alguém** fale, e o serviço tentará identificar essa pessoa entre os seus perfis de voz inscritos.

Este exemplo devolve apenas o jogo mais próximo e é a pontuação de semelhança, mas você pode obter a resposta completa que inclui as cinco melhores pontuações de semelhança adicionando `string json = result.Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)` à sua `SpeakerIdentification` função.

## <a name="changing-audio-input-type"></a>Alteração do tipo de entrada de áudio

Os exemplos deste artigo utilizam o microfone do dispositivo predefinido como entrada para amostras de áudio. No entanto, em cenários em que é necessário utilizar ficheiros áudio em vez de entrada de microfone, basta alterar qualquer instância para mudar para uma entrada de `AudioConfig.FromDefaultMicrophoneInput()` `AudioConfig.FromWavFileInput(path/to/your/file.wav)` ficheiro. Também pode ter entradas mistas, utilizando um microfone para inscrição e ficheiros para verificação, por exemplo.

## <a name="deleting-voice-profile-enrollments"></a>Apagar inscrições de perfil de voz

Para eliminar um perfil inscrito, utilize a `DeleteProfileAsync()` função no `VoiceProfileClient` objeto. A função exemplo a seguir mostra como eliminar um perfil de voz de um ID de perfil de voz conhecido.

```csharp
public static async Task DeleteProfile(SpeechConfig config, string profileId) 
{
    using (var client = new VoiceProfileClient(config))
    {
        var profile = new VoiceProfile(profileId);
        await client.DeleteProfileAsync(profile);
    }
}
```
