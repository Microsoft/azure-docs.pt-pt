---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 1b54d0059e68c7e06904155fdd1a70782e07f493
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91377180"
---
Uma das características centrais do serviço de fala é a capacidade de reconhecer a fala humana e traduzi-la para outras línguas. Neste arranque rápido aprende-se a usar o Speech SDK nas suas apps e produtos para realizar tradução de fala de alta qualidade. Este arranque rápido abrange temas como:

* Tradução de discurso a texto
* Traduzindo o discurso para várias línguas-alvo
* Realização de tradução direta do discurso para a fala

## <a name="skip-to-samples-on-github"></a>Salte para as amostras no GitHub

Se quiser saltar diretamente para o código de amostra, consulte as [amostras de arranque rápido C++](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/translate-speech-to-text) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem uma conta Azure e subscrição do serviço de fala. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço Desemação](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instale o SDK de discurso

Antes de poder fazer qualquer coisa, terá de instalar o SDK do Discurso. Dependendo da sua plataforma, siga as instruções na secção <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Get the Speech SDK <span class="docon docon-navigate-external x-hidden-focus"></span> </a> do artigo Sobre o _SDK de discurso._

## <a name="import-dependencies"></a>Dependências de importação

Para executar os exemplos neste artigo, inclua o seguinte `#include` e `using` as declarações no topo do ficheiro de código C++.

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

## <a name="sensitive-data-and-environment-variables"></a>Dados sensíveis e variáveis ambientais

O código-fonte exemplo neste artigo depende de variáveis ambientais para armazenar dados sensíveis, como a chave de subscrição de recursos da Fala e a região. O ficheiro de código C++ contém dois valores de cadeia que são atribuídos a partir das variáveis ambientais das máquinas hospedeiras, nomeadamente `SPEECH__SUBSCRIPTION__KEY` e `SPEECH__SERVICE__REGION` . Ambos os campos estão no âmbito da classe, tornando-os acessíveis dentro dos órgãos metodológicos da classe. Para obter mais informações sobre variáveis ambientais, consulte [variáveis ambientais e configuração de aplicação.](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");
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

Vamos ver como um [`SpeechTranslationConfig`][config] é criado usando uma chave e região. Consulte a página de apoio da [região](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) para encontrar o seu identificador da região.

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

## <a name="change-source-language"></a>Alterar linguagem de origem

Uma tarefa comum de tradução da fala é especificar a linguagem de entrada (ou origem). Vamos ver como mudaria a língua de entrada para italiana. No seu código, interaja com o [`SpeechTranslationConfig`][config] caso, chamando o `SetSpeechRecognitionLanguage` método.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig->SetSpeechRecognitionLanguage("it-IT");
}
```

A [`SpeechRecognitionLanguage`][recognitionlang] propriedade espera uma cadeia de formato idioma-local. Pode fornecer qualquer valor na coluna **Locale** na lista de [locais/idiomas suportados.](../../../language-support.md)

## <a name="add-translation-language"></a>Adicionar linguagem de tradução

Outra tarefa comum da tradução da fala é especificar as línguas de tradução-alvo, pelo menos uma é necessária, mas os múltiplos são apoiados. O seguinte código de corte define o francês e o alemão como alvos de linguagem de tradução.

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

A cada chamada [`AddTargetLanguage`][addlang] para, é especificada uma nova linguagem de tradução-alvo. Por outras palavras, quando a fala é reconhecida a partir da língua de origem, cada tradução-alvo está disponível como parte da operação de tradução resultante.

## <a name="initialize-a-translation-recognizer"></a>Inicialize um reconhecimento de tradução

Depois de criar um [`SpeechTranslationConfig`][config] , o próximo passo é inicializar um [`TranslationRecognizer`][recognizer] . Quando rubricar [`TranslationRecognizer`][recognizer] um, terá de passar o seu `translationConfig` . O objeto de configuração fornece as credenciais que o serviço de fala requer para validar o seu pedido.

Se está a reconhecer a fala usando o microfone padrão do seu dispositivo, eis como [`TranslationRecognizer`][recognizer] deve ser:

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

Se quiser especificar o dispositivo de entrada de áudio, terá de criar um [`AudioConfig`][audioconfig] e fornecer o parâmetro ao `audioConfig` rubricar o seu [`TranslationRecognizer`][recognizer] .

> [!TIP]
> [Saiba como obter o ID do dispositivo para o seu dispositivo de entrada de áudio](../../../how-to-select-audio-input-devices.md).

Primeiro, vai fazer referência ao objeto da `AudioConfig` seguinte forma:

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

Se quiser fornecer um ficheiro áudio em vez de utilizar um microfone, ainda terá de fornecer um `audioConfig` . No entanto, quando criar um [`AudioConfig`][audioconfig] , em vez de ligar , vai ligar e passar o `FromDefaultMicrophoneInput` `FromWavFileInput` `filename` parâmetro.

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

Para traduzir a fala, o SDK do Discurso baseia-se num microfone ou numa entrada de ficheiro sonoro. O reconhecimento da fala ocorre antes da tradução da fala. Depois de todos os objetos terem sido inicializados, chame a função de reconhecimento uma vez e obtenha o resultado.

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

Para obter mais informações sobre discurso-a-texto, consulte [os fundamentos do reconhecimento da fala.](../../../speech-to-text-basics.md)

## <a name="synthesize-translations"></a>Sintetizar traduções

Após um reconhecimento e tradução de voz bem sucedido, o resultado contém todas as traduções num dicionário. A [`Translations`][translations] chave do dicionário é a linguagem de tradução alvo e o valor é o texto traduzido. A fala reconhecida pode ser traduzida e, em seguida, sintetizada numa língua diferente (fala-a-discurso).

### <a name="event-based-synthesis"></a>Síntese baseada em eventos

O `TranslationRecognizer` objeto expõe um `Synthesizing` evento. O evento dispara várias vezes e fornece um mecanismo para recuperar o áudio sintetizado do resultado do reconhecimento de tradução. Se estiver a traduzir para várias línguas, consulte [a síntese manual.](#manual-synthesis) Especifique a voz da síntese atribuindo a [`SetVoiceName`][voicename] e forneça um manipulador de eventos para o `Synthesizing` evento, obtenha o áudio. O exemplo a seguir guarda o áudio traduzido como um ficheiro *.wav.*

> [!IMPORTANT]
> A síntese baseada em eventos funciona apenas com uma única tradução, **não** adiciona várias línguas de tradução alvo. Além disso, [`SetVoiceName`][voicename] a língua deve ser a mesma língua que a língua de tradução-alvo, por exemplo; `"de"` poderia mapear para `"de-DE-Hedda"` .

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

O [`Translations`][translations] dicionário pode ser utilizado para sintetizar o áudio a partir do texto de tradução. Iterar através de cada tradução, e sintetizar a tradução. Ao criar um `SpeechSynthesizer` caso, o `SpeechConfig` objeto precisa de ter a sua propriedade definida para a voz [`SetSpeechSynthesisVoiceName`][speechsynthesisvoicename] desejada. O exemplo a seguir traduz-se em cinco línguas, e cada tradução é então sintetizada para um ficheiro áudio na língua neural correspondente.

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
