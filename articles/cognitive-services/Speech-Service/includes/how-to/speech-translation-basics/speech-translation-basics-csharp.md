---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 798db8e438a78fb818bd0af18f84c87c1f9ceb84
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105475"
---
Uma das características centrais do serviço de fala é a capacidade de reconhecer a fala humana e traduzi-la para outras línguas. Neste arranque rápido aprende-se a usar o Speech SDK nas suas apps e produtos para realizar tradução de fala de alta qualidade. Este arranque rápido abrange temas como:

* Tradução de discurso a texto
* Traduzindo o discurso para várias línguas-alvo
* Realização de tradução direta do discurso para a fala

## <a name="skip-to-samples-on-github"></a>Salte para as amostras no GitHub

Se quiser saltar diretamente para o código de amostra, consulte as [amostras de arranque rápido C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/translate-speech-to-text) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem uma conta Azure e subscrição do serviço de fala. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço Desemação](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instale o SDK de discurso

Antes de poder fazer qualquer coisa, terá de instalar o SDK do Discurso. Dependendo da sua plataforma, siga as instruções na secção <a href="/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Get the Speech SDK</a> do artigo _Sobre o SDK de discurso._

## <a name="import-dependencies"></a>Dependências de importação

Para executar os exemplos neste artigo, inclua as seguintes `using` declarações no topo do ficheiro *.cs Programa.*

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Dados sensíveis e variáveis ambientais

O código-fonte exemplo neste artigo depende de variáveis ambientais para armazenar dados sensíveis, como a chave de subscrição de recursos da Fala e a região. A `Program` classe contém dois `static readonly string` valores que são atribuídos a partir das variáveis ambientais das máquinas hospedeiras, nomeadamente `SPEECH__SUBSCRIPTION__KEY` e `SPEECH__SERVICE__REGION` . Ambos os campos estão no âmbito da classe, tornando-os acessíveis dentro dos órgãos metodológicos da classe. Para obter mais informações sobre variáveis ambientais, consulte [variáveis ambientais e configuração de aplicação.](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => Task.CompletedTask;
}
```

## <a name="create-a-speech-translation-configuration"></a>Criar uma configuração de tradução de discurso

Para ligar para o serviço de discurso usando o SDK de discurso, precisa de criar um [`SpeechTranslationConfig`][config] . Esta classe inclui informações sobre a sua subscrição, como a sua chave e região associada, ponto final, anfitrião ou token de autorização.

> [!TIP]
> Independentemente de estar a realizar reconhecimento de voz, síntese de fala, tradução ou reconhecimento de intenções, irá sempre criar uma configuração.

Há algumas maneiras de inicializar [`SpeechTranslationConfig`][config] um:

* Com uma subscrição: passe numa chave e na região associada.
* Com um ponto final: passe num ponto final de serviço de discurso. Uma chave ou sinal de autorização é opcional.
* Com um anfitrião: passe em um endereço de anfitrião. Uma chave ou sinal de autorização é opcional.
* Com um sinal de autorização: passe em um token de autorização e na região associada.

Vamos ver como um [`SpeechTranslationConfig`][config] é criado usando uma chave e região. Obtenha estas credenciais seguindo os passos no [serviço Try the Speech gratuitamente](../../../overview.md#try-the-speech-service-for-free).

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => TranslateSpeechAsync();

    static async Task TranslateSpeechAsync()
    {
        var translationConfig =
            SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Alterar linguagem de origem

Uma tarefa comum de tradução da fala é especificar a linguagem de entrada (ou origem). Vamos ver como mudaria a língua de entrada para italiana. No seu código, interaja com a [`SpeechTranslationConfig`][config] instância, atribuindo-se à `SpeechRecognitionLanguage` propriedade.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig.SpeechRecognitionLanguage = "it-IT";
}
```

A [`SpeechRecognitionLanguage`][recognitionlang] propriedade espera uma cadeia de formato idioma-local. Pode fornecer qualquer valor na coluna **Locale** na lista de [locais/idiomas suportados.](../../../language-support.md)

## <a name="add-translation-language"></a>Adicionar linguagem de tradução

Outra tarefa comum da tradução da fala é especificar as línguas de tradução-alvo, pelo menos uma é necessária, mas os múltiplos são apoiados. O seguinte código de corte define o francês e o alemão como alvos de linguagem de tradução.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig.SpeechRecognitionLanguage = "it-IT";
    
    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.AddTargetLanguage("fr");
    translationConfig.AddTargetLanguage("de");
}
```

A cada chamada [`AddTargetLanguage`][addlang] para, é especificada uma nova linguagem de tradução-alvo. Por outras palavras, quando a fala é reconhecida a partir da língua de origem, cada tradução-alvo está disponível como parte da operação de tradução resultante.

## <a name="initialize-a-translation-recognizer"></a>Inicialize um reconhecimento de tradução

Depois de criar um [`SpeechTranslationConfig`][config] , o próximo passo é inicializar um [`TranslationRecognizer`][recognizer] . Quando rubricar [`TranslationRecognizer`][recognizer] um, terá de passar o seu `translationConfig` . O objeto de configuração fornece as credenciais que o serviço de fala requer para validar o seu pedido.

Se está a reconhecer a fala usando o microfone padrão do seu dispositivo, eis como [`TranslationRecognizer`][recognizer] deve ser:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);
}
```

Se quiser especificar o dispositivo de entrada de áudio, terá de criar um [`AudioConfig`][audioconfig] e fornecer o parâmetro ao `audioConfig` rubricar o seu [`TranslationRecognizer`][recognizer] .

> [!TIP]
> [Saiba como obter o ID do dispositivo para o seu dispositivo de entrada de áudio](../../../how-to-select-audio-input-devices.md).

Primeiro, vai fazer referência ao objeto da `AudioConfig` seguinte forma:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

Se quiser fornecer um ficheiro áudio em vez de utilizar um microfone, ainda terá de fornecer um `audioConfig` . No entanto, quando criar um [`AudioConfig`][audioconfig] , em vez de ligar , vai ligar e passar o `FromDefaultMicrophoneInput` `FromWavFileInput` `filename` parâmetro.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Traduzir voz

Para traduzir a fala, o SDK do Discurso baseia-se num microfone ou numa entrada de ficheiro sonoro. O reconhecimento da fala ocorre antes da tradução da fala. Depois de todos os objetos terem sido inicializados, chame a função de reconhecimento uma vez e obtenha o resultado.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");
    
    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\":");
        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");
        }
    }
}
```

Para obter mais informações sobre discurso-a-texto, consulte [os fundamentos do reconhecimento da fala.](../../../get-started-speech-to-text.md)

## <a name="synthesize-translations"></a>Sintetizar traduções

Após um reconhecimento e tradução de voz bem sucedido, o resultado contém todas as traduções num dicionário. A [`Translations`][translations] chave do dicionário é a linguagem de tradução alvo e o valor é o texto traduzido. A fala reconhecida pode ser traduzida e, em seguida, sintetizada numa língua diferente (fala-a-discurso).

### <a name="event-based-synthesis"></a>Síntese baseada em eventos

O `TranslationRecognizer` objeto expõe um `Synthesizing` evento. O evento dispara várias vezes e fornece um mecanismo para recuperar o áudio sintetizado do resultado do reconhecimento de tradução. Se estiver a traduzir para várias línguas, consulte [a síntese manual.](#manual-synthesis) Especifique a voz da síntese atribuindo a [`VoiceName`][voicename] e forneça um manipulador de eventos para o `Synthesizing` evento, obtenha o áudio. O exemplo a seguir guarda o áudio traduzido como um ficheiro *.wav.*

> [!IMPORTANT]
> A síntese baseada em eventos funciona apenas com uma única tradução, **não** adiciona várias línguas de tradução alvo. Além disso, [`VoiceName`][voicename] a língua deve ser a mesma língua que a língua de tradução-alvo, por exemplo; `"de"` poderia mapear para `"de-DE-Hedda"` .

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguage = "de";
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    translationConfig.AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.VoiceName = "de-DE-Hedda";

    using var recognizer = new TranslationRecognizer(translationConfig);

    recognizer.Synthesizing += (_, e) =>
    {
        var audio = e.Result.GetAudio();
        Console.WriteLine($"Audio synthesized: {audio.Length:#,0} byte(s) {(audio.Length == 0 ? "(Complete)" : "")}");

        if (audio.Length > 0)
        {
            File.WriteAllBytes("YourAudioFile.wav", audio);
        }
    };

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{toLanguage}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\"");
        Console.WriteLine($"Translated into '{toLanguage}': {result.Translations[toLanguage]}");
    }
}
```

### <a name="manual-synthesis"></a>Síntese manual

O [`Translations`][translations] dicionário pode ser utilizado para sintetizar o áudio a partir do texto de tradução. Iterar através de cada tradução, e sintetizar a tradução. Ao criar um `SpeechSynthesizer` caso, o `SpeechConfig` objeto precisa de ter a sua propriedade definida para a voz [`SpeechSynthesisVoiceName`][speechsynthesisvoicename] desejada. O exemplo a seguir traduz-se em cinco línguas, e cada tradução é então sintetizada para um ficheiro áudio na língua neural correspondente.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        var languageToVoiceMap = new Dictionary<string, string>
        {
            ["de"] = "de-DE-KatjaNeural",
            ["en"] = "en-US-AriaNeural",
            ["it"] = "it-IT-ElsaNeural",
            ["pt"] = "pt-BR-FranciscaNeural",
            ["zh-Hans"] = "zh-CN-XiaoxiaoNeural"
        };

        Console.WriteLine($"Recognized: \"{result.Text}\"");

        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");

            var speechConfig =
                SpeechConfig.FromSubscription(
                    SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);
            speechConfig.SpeechSynthesisVoiceName = languageToVoiceMap[language];

            using var audioConfig = AudioConfig.FromWavFileOutput($"{language}-translation.wav");
            using var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
            
            await synthesizer.SpeakTextAsync(translation);
        }
    }
}
```

Para obter mais informações sobre a síntese da fala, consulte [os fundamentos da síntese da fala.](../../../get-started-text-to-speech.md)

[config]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig
[audioconfig]: /dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig
[recognizer]: /dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognizer
[recognitionlang]: /dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage
[addlang]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.addtargetlanguage
[translations]: /dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognitionresult.translations
[voicename]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.voicename
[speechsynthesisvoicename]: /dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechsynthesisvoicename