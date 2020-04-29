---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: bac2ed447c9055f095e604725591c487378f5091
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399654"
---
## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que tem uma subscrição de serviço de conta Azure e Speech. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço De Fala.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>Instale o SDK do Discurso

Antes de poder fazer qualquer coisa, terá de instalar o SDK do Discurso. Dependendo da sua plataforma, siga as instruções sob a secção <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Get the Speech SDK <span class="docon docon-navigate-external x-hidden-focus"></span> </a> do artigo SDK do Discurso.

## <a name="import-dependencies"></a>Dependências de importação

Para executar os exemplos neste artigo, inclua as seguintes `using` declarações no topo do ficheiro *Program.cs.*

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

## <a name="sensitive-data-and-environment-variables"></a>Variáveis sensíveis de dados e ambiente

O código fonte de exemplo neste artigo depende de variáveis ambientais para armazenar dados sensíveis, como a chave de subscrição de recursos da Fala e região. A `Program` classe `static readonly string` contém dois valores que são atribuídos a `SPEECH__SUBSCRIPTION__KEY` partir `SPEECH__SERVICE__REGION`das variáveis ambientais das máquinas hospedeiras, nomeadamente e . Ambos os campos estão no âmbito da classe, tornando-os acessíveis dentro dos corpos metodológicos da classe. Para obter mais informações sobre variáveis ambientais, consulte [variáveis ambientais e configuração de aplicações.](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)

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

## <a name="create-a-speech-translation-configuration"></a>Criar uma configuração de tradução de fala

Para chamar o serviço de Fala usando o SDK do Discurso, é necessário criar um [`SpeechTranslationConfig`][config]. Esta aula inclui informações sobre a sua subscrição, como a sua chave e região associada, ponto final, anfitrião ou sinal de autorização.

> [!TIP]
> Independentemente de estar a realizar reconhecimento de discurso, síntese de fala, tradução ou reconhecimento de intenções, irá sempre criar uma configuração.

Há algumas maneiras de inicializar um: [`SpeechTranslationConfig`][config]

* Com uma subscrição: passe numa chave e na região associada.
* Com um ponto final: passe num ponto final do serviço da Fala. Uma chave ou ficha de autorização é opcional.
* Com um anfitrião: passe num endereço de anfitrião. Uma chave ou ficha de autorização é opcional.
* Com um símbolo de autorização: passe num símbolo de autorização e na região associada.

Vamos ver como um [`SpeechTranslationConfig`][config] é criado usando uma chave e região. Consulte a página de apoio da [região](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) para encontrar o seu identificador de região.

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

## <a name="change-source-language"></a>Alterar a linguagem de origem

Uma tarefa comum de tradução da fala é especificar a linguagem de entrada (ou fonte). Vamos ver como mudarias a linguagem de entrada para italiano. No seu código, [`SpeechTranslationConfig`][config] interaja com `SpeechRecognitionLanguage` a instância, atribuindo à propriedade.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig.SpeechRecognitionLanguage = "it-IT";
}
```

A [`SpeechRecognitionLanguage`][recognitionlang] propriedade espera uma cadeia de formato local de linguagem. Pode fornecer qualquer valor na coluna **Locale** na lista de [locais/línguas](../../../language-support.md)suportados.

## <a name="add-translation-language"></a>Adicionar linguagem de tradução

Outra tarefa comum da tradução da fala é especificar as línguas de tradução-alvo, pelo menos uma é necessária, mas os múltiplos são suportados. No código seguinte, tanto o francês como o alemão como alvos de língua de tradução.

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

A cada [`AddTargetLanguage`][addlang]chamada para , é especificada uma nova linguagem de tradução-alvo. Por outras palavras, quando a fala é reconhecida a partir da linguagem fonte, cada tradução-alvo está disponível como parte da operação de tradução resultante.

## <a name="initialize-a-translation-recognizer"></a>Inicializar um reconhecimento de tradução

Depois de ter [`SpeechTranslationConfig`][config]criado um , o próximo [`TranslationRecognizer`][recognizer]passo é inicializar um . Quando rubricar [`TranslationRecognizer`][recognizer]um, terá de passar `translationConfig`o seu. O objeto de configuração fornece as credenciais que o serviço de fala requer para validar o seu pedido.

Se está a reconhecer a fala usando o microfone padrão [`TranslationRecognizer`][recognizer] do seu dispositivo, eis como deve ser:

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

Se pretender especificar o dispositivo de entrada de áudio, [`AudioConfig`][audioconfig] terá de `audioConfig` criar um e [`TranslationRecognizer`][recognizer]fornecer o parâmetro ao rubricar o seu .

> [!TIP]
> [Aprenda a obter o ID do dispositivo para o seu dispositivo](../../../how-to-select-audio-input-devices.md)de entrada de áudio .

Primeiro, irá referir `AudioConfig` o objeto da seguinte forma:

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

Se pretender fornecer um ficheiro áudio em vez de utilizar um `audioConfig`microfone, ainda terá de fornecer um . No entanto, [`AudioConfig`][audioconfig]quando criar `FromDefaultMicrophoneInput`um , em `FromWavFileInput` vez `filename` de ligar, ligará e passará o parâmetro.

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

Para traduzir o discurso, o SDK do Discurso baseia-se num microfone ou numa entrada de ficheiros áudio. O reconhecimento da fala ocorre antes da tradução da fala. Depois de todos os objetos terem sido inicializados, ligue para a função reconhecer uma vez e obtenha o resultado.

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

Para obter mais informações sobre o discurso ao texto, consulte [os fundamentos do reconhecimento da fala.](../../../speech-to-text-basics.md)

## <a name="synthesize-translations"></a>Traduções sintetizadoras

Após um reconhecimento e tradução bem-sucedidos da fala, o resultado contém todas as traduções num dicionário. A [`Translations`][translations] chave do dicionário é a linguagem de tradução-alvo e o valor é o texto traduzido. O discurso reconhecido pode ser traduzido, depois sintetizado numa linguagem diferente (discurso a fala).

### <a name="event-based-synthesis"></a>Síntese baseada em eventos

O `TranslationRecognizer` objeto expõe `Synthesizing` um evento. O evento dispara várias vezes, e fornece um mecanismo para recuperar o áudio sintetizado do resultado do reconhecimento de tradução. Se estiver a traduzir para várias línguas, consulte a [síntese manual.](#manual-synthesis) Especifique a voz [`VoiceName`][voicename] de síntese atribuindo `Synthesizing` um e forneça um manipulador de eventos para o evento, obtenha o áudio. O exemplo seguinte guarda o áudio traduzido como um ficheiro *.wav.*

> [!IMPORTANT]
> A síntese baseada em eventos funciona apenas com uma única tradução, **não** adiciona múltipis de tradução de alvos. Além disso, deve ser a [`VoiceName`][voicename] mesma língua que a língua de tradução-alvo, por exemplo; `"de"` poderia mapear `"de-DE-Hedda"`para .

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

O [`Translations`][translations] dicionário pode ser usado para sintetizar áudio a partir do texto de tradução. Iterar através de cada tradução, e sintetizar a tradução. Ao criar `SpeechSynthesizer` uma `SpeechConfig` instância, o [`SpeechSynthesisVoiceName`][speechsynthesisvoicename] objeto precisa de ter a sua propriedade definida para a voz desejada. O exemplo seguinte traduz-se em cinco línguas, e cada tradução é então sintetizada para um ficheiro áudio na língua neural correspondente.

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

Para obter mais informações sobre a síntese da fala, consulte [os fundamentos da síntese da fala.](../../../text-to-speech-basics.md)

[config]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig?view=azure-dotnet
[audioconfig]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet
[recognizer]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognizer?view=azure-dotnet
[recognitionlang]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?view=azure-dotnet
[addlang]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.addtargetlanguage?view=azure-dotnet
[translations]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognitionresult.translations?view=azure-dotnet
[voicename]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.voicename?view=azure-dotnet
[speechsynthesisvoicename]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechsynthesisvoicename?view=azure-dotnet
