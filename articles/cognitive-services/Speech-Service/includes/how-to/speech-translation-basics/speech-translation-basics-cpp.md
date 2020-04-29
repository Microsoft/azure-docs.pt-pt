---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 09f08e314a634de13a683440ad9fead97ad8a260
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399613"
---
## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que tem uma subscrição de serviço de conta Azure e Speech. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço De Fala.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>Instale o SDK do Discurso

Antes de poder fazer qualquer coisa, terá de instalar o SDK do Discurso. Dependendo da sua plataforma, siga as instruções sob a secção <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Get the Speech SDK <span class="docon docon-navigate-external x-hidden-focus"></span> </a> do artigo SDK do Discurso.

## <a name="import-dependencies"></a>Dependências de importação

Para executar os exemplos deste artigo, inclua os seguintes `#include` e `using` declarações no topo do ficheiro de código C++.

```cpp
#include <iostream> // cin, cout
#include <fstream>
#include <string>
#include <stdio.h>
#include <stdlib.h>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
using namespace Microsoft::CognitiveServices::Speech::Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Variáveis sensíveis de dados e ambiente

O código fonte de exemplo neste artigo depende de variáveis ambientais para armazenar dados sensíveis, como a chave de subscrição de recursos da Fala e região. O ficheiro de código C++ contém dois valores de cadeia que `SPEECH__SUBSCRIPTION__KEY` são `SPEECH__SERVICE__REGION`atribuídos a partir das variáveis ambientais das máquinas hospedeiras, nomeadamente e . Ambos os campos estão no âmbito da classe, tornando-os acessíveis dentro dos corpos metodológicos da classe. Para obter mais informações sobre variáveis ambientais, consulte [variáveis ambientais e configuração de aplicações.](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");
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

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");

void translateSpeech() {
    auto config =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
}

int main(int argc, char** argv) {
    setlocale(LC_ALL, "");
    translateSpeech();
    return 0;
}
```

## <a name="change-source-language"></a>Alterar a linguagem de origem

Uma tarefa comum de tradução da fala é especificar a linguagem de entrada (ou fonte). Vamos ver como mudarias a linguagem de entrada para italiano. No seu código, [`SpeechTranslationConfig`][config] interaja `SetSpeechRecognitionLanguage` com a instância, chamando o método.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig->SetSpeechRecognitionLanguage("it-IT");
}
```

A [`SpeechRecognitionLanguage`][recognitionlang] propriedade espera uma cadeia de formato local de linguagem. Pode fornecer qualquer valor na coluna **Locale** na lista de [locais/línguas](../../../language-support.md)suportados.

## <a name="add-translation-language"></a>Adicionar linguagem de tradução

Outra tarefa comum da tradução da fala é especificar as línguas de tradução-alvo, pelo menos uma é necessária, mas os múltiplos são suportados. No código seguinte, tanto o francês como o alemão como alvos de língua de tradução.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig->SetSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig->AddTargetLanguage("fr");
    translationConfig->AddTargetLanguage("de");
}
```

A cada [`AddTargetLanguage`][addlang]chamada para , é especificada uma nova linguagem de tradução-alvo. Por outras palavras, quando a fala é reconhecida a partir da linguagem fonte, cada tradução-alvo está disponível como parte da operação de tradução resultante.

## <a name="initialize-a-translation-recognizer"></a>Inicializar um reconhecimento de tradução

Depois de ter [`SpeechTranslationConfig`][config]criado um , o próximo [`TranslationRecognizer`][recognizer]passo é inicializar um . Quando rubricar [`TranslationRecognizer`][recognizer]um, terá de passar `translationConfig`o seu. O objeto de configuração fornece as credenciais que o serviço de fala requer para validar o seu pedido.

Se está a reconhecer a fala usando o microfone padrão [`TranslationRecognizer`][recognizer] do seu dispositivo, eis como deve ser:

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
}
```

Se pretender especificar o dispositivo de entrada de áudio, [`AudioConfig`][audioconfig] terá de `audioConfig` criar um e [`TranslationRecognizer`][recognizer]fornecer o parâmetro ao rubricar o seu .

> [!TIP]
> [Aprenda a obter o ID do dispositivo para o seu dispositivo](../../../how-to-select-audio-input-devices.md)de entrada de áudio .

Primeiro, irá referir `AudioConfig` o objeto da seguinte forma:

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

Se pretender fornecer um ficheiro áudio em vez de utilizar um `audioConfig`microfone, ainda terá de fornecer um . No entanto, [`AudioConfig`][audioconfig]quando criar `FromDefaultMicrophoneInput`um , em `FromWavFileInput` vez `filename` de ligar, ligará e passará o parâmetro.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromWavFileInput("YourAudioFile.wav");
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Traduzir voz

Para traduzir o discurso, o SDK do Discurso baseia-se num microfone ou numa entrada de ficheiros áudio. O reconhecimento da fala ocorre antes da tradução da fala. Depois de todos os objetos terem sido inicializados, ligue para a função reconhecer uma vez e obtenha o resultado.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    string fromLanguage = "en-US";
    string toLanguages[3] = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

Para obter mais informações sobre o discurso ao texto, consulte [os fundamentos do reconhecimento da fala.](../../../speech-to-text-basics.md)

## <a name="synthesize-translations"></a>Traduções sintetizadoras

Após um reconhecimento e tradução bem-sucedidos da fala, o resultado contém todas as traduções num dicionário. A [`Translations`][translations] chave do dicionário é a linguagem de tradução-alvo e o valor é o texto traduzido. O discurso reconhecido pode ser traduzido, depois sintetizado numa linguagem diferente (discurso a fala).

### <a name="event-based-synthesis"></a>Síntese baseada em eventos

O `TranslationRecognizer` objeto expõe `Synthesizing` um evento. O evento dispara várias vezes, e fornece um mecanismo para recuperar o áudio sintetizado do resultado do reconhecimento de tradução. Se estiver a traduzir para várias línguas, consulte a [síntese manual.](#manual-synthesis) Especifique a voz [`SetVoiceName`][voicename] de síntese atribuindo `Synthesizing` um e forneça um manipulador de eventos para o evento, obtenha o áudio. O exemplo seguinte guarda o áudio traduzido como um ficheiro *.wav.*

> [!IMPORTANT]
> A síntese baseada em eventos funciona apenas com uma única tradução, **não** adiciona múltipis de tradução de alvos. Além disso, deve ser a [`SetVoiceName`][voicename] mesma língua que a língua de tradução-alvo, por exemplo; `"de"` poderia mapear `"de-DE-Hedda"`para .

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguage = "de";
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    translationConfig->AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig->SetVoiceName("de-DE-Hedda");

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    recognizer->Synthesizing.Connect([](const TranslationSynthesisEventArgs& e)
        {
            auto audio = e.Result->Audio;
            auto size = audio.size();
            cout << "Audio synthesized: " << size << " byte(s)" << (size == 0 ? "(COMPLETE)" : "") << std::endl;

            if (size > 0) {
                ofstream file("translation.wav", ios::out | ios::binary);
                auto audioData = audio.data();
                file.write((const char*)audioData, sizeof(audio[0]) * size);
                file.close();
            }
        });

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

### <a name="manual-synthesis"></a>Síntese manual

O [`Translations`][translations] dicionário pode ser usado para sintetizar áudio a partir do texto de tradução. Iterar através de cada tradução, e sintetizar a tradução. Ao criar `SpeechSynthesizer` uma `SpeechConfig` instância, o [`SetSpeechSynthesisVoiceName`][speechsynthesisvoicename] objeto precisa de ter a sua propriedade definida para a voz desejada. O exemplo seguinte traduz-se em cinco línguas, e cada tradução é então sintetizada para um ficheiro áudio na língua neural correspondente.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        map<string, string> languageToVoiceMap;
        languageToVoiceMap["de"] = "de-DE-KatjaNeural";
        languageToVoiceMap["en"] = "en-US-AriaNeural";
        languageToVoiceMap["it"] = "it-IT-ElsaNeural";
        languageToVoiceMap["pt"] = "pt-BR-FranciscaNeural";
        languageToVoiceMap["zh-Hans"] = "zh-CN-XiaoxiaoNeural";

        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;

            auto speech_config =
                SpeechConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
            speech_config->SetSpeechSynthesisVoiceName(languageToVoiceMap[language]);

            auto audio_config = AudioConfig::FromWavFileOutput(language + "-translation.wav");
            auto synthesizer = SpeechSynthesizer::FromConfig(speech_config, audio_config);

            synthesizer->SpeakTextAsync(translation).get();
        }
    }
}
```

Para obter mais informações sobre a síntese da fala, consulte [os fundamentos da síntese da fala.](../../../text-to-speech-basics.md)

[config]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig
[audioconfig]: https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig
[recognizer]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognizer
[recognitionlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage
[addlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#addtargetlanguage
[translations]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognitionresult#translations
[voicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#setvoicename
[speechsynthesisvoicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechsynthesisvoicename
