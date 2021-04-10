---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.custom: devx-track-java
ms.author: trbye
ms.openlocfilehash: e030e6466570d3fc2abf809ca09d46c9f596b765
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105104523"
---
Uma das características centrais do serviço de fala é a capacidade de reconhecer a fala humana e traduzi-la para outras línguas. Neste arranque rápido aprende-se a usar o Speech SDK nas suas apps e produtos para realizar tradução de fala de alta qualidade. Este arranque rápido abrange temas como:

* Tradução de discurso a texto
* Traduzindo o discurso para várias línguas-alvo
* Realização de tradução direta do discurso para a fala

## <a name="skip-to-samples-on-github"></a>Salte para as amostras no GitHub

Se quiser saltar diretamente para o código de amostra, consulte as [amostras de arranque rápido](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre/translate-speech-to-text) de Java no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem uma conta Azure e subscrição do serviço de fala. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço Desemação](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instale o SDK de discurso

Antes de poder fazer qualquer coisa, terá de instalar o SDK do Discurso. Dependendo da sua plataforma, siga as instruções na secção <a href="/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Get the Speech SDK</a> do artigo _Sobre o SDK de discurso._

## <a name="import-dependencies"></a>Dependências de importação

Para executar os exemplos neste artigo, inclua as `import` seguintes declarações no topo do **. Ficheiro de* código Java.

```java
package speech;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.*;
import com.microsoft.cognitiveservices.speech.translation.*;
```

## <a name="sensitive-data-and-environment-variables"></a>Dados sensíveis e variáveis ambientais

O código-fonte exemplo neste artigo depende de variáveis ambientais para armazenar dados sensíveis, como a chave de subscrição de recursos da Fala e a região. O ficheiro de código Java contém dois `static final String` valores que são atribuídos a partir das variáveis ambientais das máquinas hospedeiras, nomeadamente `SPEECH__SUBSCRIPTION__KEY` e `SPEECH__SERVICE__REGION` . Ambos os campos estão no âmbito da classe, tornando-os acessíveis dentro dos órgãos metodológicos da classe. Para obter mais informações sobre variáveis ambientais, consulte [variáveis ambientais e configuração de aplicação.](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SUBSCRIPTION__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) { }
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

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SERVICE__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) {
        try {
            translateSpeech();
            System.exit(0);
        } catch (Exception ex) {
            System.out.println(ex);
            System.exit(1);
        }
    }

    static void translateSpeech() {
        SpeechTranslationConfig config = SpeechTranslationConfig.fromSubscription(
            SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Alterar linguagem de origem

Uma tarefa comum de tradução da fala é especificar a linguagem de entrada (ou origem). Vamos ver como mudaria a língua de entrada para italiana. No seu código, interaja com o [`SpeechTranslationConfig`][config] caso, chamando o `setSpeechRecognitionLanguage` método.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    // Source (input) language
    translationConfig.setSpeechRecognitionLanguage("it-IT");
}
```

A [`setSpeechRecognitionLanguage`][recognitionlang] função espera uma cadeia de formato idioma-local. Pode fornecer qualquer valor na coluna **Locale** na lista de [locais/idiomas suportados.](../../../language-support.md)

## <a name="add-translation-language"></a>Adicionar linguagem de tradução

Outra tarefa comum da tradução da fala é especificar as línguas de tradução-alvo, pelo menos uma é necessária, mas os múltiplos são apoiados. O seguinte código de corte define o francês e o alemão como alvos de linguagem de tradução.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    translationConfig.setSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.addTargetLanguage("fr");
    translationConfig.addTargetLanguage("de");
}
```

A cada chamada [`addTargetLanguage`][addlang] para, é especificada uma nova linguagem de tradução-alvo. Por outras palavras, quando a fala é reconhecida a partir da língua de origem, cada tradução-alvo está disponível como parte da operação de tradução resultante.

## <a name="initialize-a-translation-recognizer"></a>Inicialize um reconhecimento de tradução

Depois de criar um [`SpeechTranslationConfig`][config] , o próximo passo é inicializar um [`TranslationRecognizer`][recognizer] . Quando rubricar [`TranslationRecognizer`][recognizer] um, terá de passar o seu `translationConfig` . O objeto de configuração fornece as credenciais que o serviço de fala requer para validar o seu pedido.

Se está a reconhecer a fala usando o microfone padrão do seu dispositivo, eis como [`TranslationRecognizer`][recognizer] deve ser:

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
    }
}
```

Se quiser especificar o dispositivo de entrada de áudio, terá de criar um [`AudioConfig`][audioconfig] e fornecer o parâmetro ao `audioConfig` rubricar o seu [`TranslationRecognizer`][recognizer] .

> [!TIP]
> [Saiba como obter o ID do dispositivo para o seu dispositivo de entrada de áudio](../../../how-to-select-audio-input-devices.md).

Primeiro, vai fazer referência ao objeto da `AudioConfig` seguinte forma:

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

Se quiser fornecer um ficheiro áudio em vez de utilizar um microfone, ainda terá de fornecer um `audioConfig` . No entanto, quando criar um [`AudioConfig`][audioconfig] , em vez de ligar , vai ligar e passar o `fromDefaultMicrophoneInput` `fromWavFileInput` `filename` parâmetro.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

## <a name="translate-speech"></a>Traduzir voz

Para traduzir a fala, o SDK do Discurso baseia-se num microfone ou numa entrada de ficheiro sonoro. O reconhecimento da fala ocorre antes da tradução da fala. Depois de todos os objetos terem sido inicializados, chame a função de reconhecimento uma vez e obtenha o resultado.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                System.out.printf("Translated into '%s': %s\n", pair.getKey(), pair.getValue());
            }
        }
    }
}
```

Para obter mais informações sobre discurso-a-texto, consulte [os fundamentos do reconhecimento da fala.](../../../get-started-speech-to-text.md)

## <a name="synthesize-translations"></a>Sintetizar traduções

Após um reconhecimento e tradução de voz bem sucedido, o resultado contém todas as traduções num dicionário. A [`getTranslations`][translations] função devolve um dicionário com a chave como a língua de tradução alvo e o valor é o texto traduzido. A fala reconhecida pode ser traduzida e, em seguida, sintetizada numa língua diferente (fala-a-discurso).

### <a name="event-based-synthesis"></a>Síntese baseada em eventos

O `TranslationRecognizer` objeto expõe um `synthesizing` evento. O evento dispara várias vezes e fornece um mecanismo para recuperar o áudio sintetizado do resultado do reconhecimento de tradução. Se estiver a traduzir para várias línguas, consulte [a síntese manual.](#manual-synthesis) Especifique a voz da síntese atribuindo a [`setVoiceName`][voicename] e forneça um manipulador de eventos para o `synthesizing` evento, obtenha o áudio. O exemplo a seguir guarda o áudio traduzido como um ficheiro *.wav.*

> [!IMPORTANT]
> A síntese baseada em eventos funciona apenas com uma única tradução, **não** adiciona várias línguas de tradução alvo. Além disso, [`setVoiceName`][voicename] a língua deve ser a mesma língua que a língua de tradução-alvo, por exemplo; `"de"` poderia mapear para `"de-DE-Hedda"` .

```java
static void translateSpeech() throws ExecutionException, FileNotFoundException, InterruptedException, IOException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    String fromLanguage = "en-US";
    String toLanguage = "de";
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    translationConfig.addTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.setVoiceName("de-DE-Hedda");

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        recognizer.synthesizing.addEventListener((s, e) -> {
            byte[] audio = e.getResult().getAudio();
            int size = audio.length;
            System.out.println("Audio synthesized: " + size + " byte(s)" + (size == 0 ? "(COMPLETE)" : ""));

            if (size > 0) {
                try (FileOutputStream file = new FileOutputStream("translation.wav")) {
                    file.write(audio);
                } catch (IOException ex) {
                    ex.printStackTrace();
                }
            }
        });

        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);
            }
        }
    }
}
```

### <a name="manual-synthesis"></a>Síntese manual

A [`getTranslations`][translations] função devolve um dicionário que pode ser usado para sintetizar áudio a partir do texto de tradução. Iterar através de cada tradução, e sintetizar a tradução. Ao criar um `SpeechSynthesizer` caso, o `SpeechConfig` objeto precisa de ter a sua propriedade definida para a voz [`setSpeechSynthesisVoiceName`][speechsynthesisvoicename] desejada. O exemplo a seguir traduz-se em cinco línguas, e cada tradução é então sintetizada para um ficheiro áudio na língua neural correspondente.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
            Map<String, String> languageToVoiceMap = new HashMap<String, String>();
            languageToVoiceMap.put("de", "de-DE-KatjaNeural");
            languageToVoiceMap.put("en", "en-US-AriaNeural");
            languageToVoiceMap.put("it", "it-IT-ElsaNeural");
            languageToVoiceMap.put("pt", "pt-BR-FranciscaNeural");
            languageToVoiceMap.put("zh-Hans", "zh-CN-XiaoxiaoNeural");

            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);

                SpeechConfig speechConfig =
                    SpeechConfig.fromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
                speechConfig.setSpeechSynthesisVoiceName(languageToVoiceMap.get(language));

                AudioConfig audioConfig = AudioConfig.fromWavFileOutput(language + "-translation.wav");
                try (SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig)) {
                    synthesizer.SpeakTextAsync(translation).get();
                }
            }
        }
    }
}
```

Para obter mais informações sobre a síntese da fala, consulte [os fundamentos da síntese da fala.](../../../get-started-text-to-speech.md)

[config]: /java/api/com.microsoft.cognitiveservices.speech.translation.SpeechTranslationConfig
[audioconfig]: /java/api/com.microsoft.cognitiveservices.speech.audio.AudioConfig
[recognizer]: /java/api/com.microsoft.cognitiveservices.speech.translation.TranslationRecognizer
[recognitionlang]: /java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage
[addlang]: /java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.addtargetlanguage
[translations]: /java/api/com.microsoft.cognitiveservices.speech.translation.translationrecognitionresult.gettranslations
[voicename]: /java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.setvoicename
[speechsynthesisvoicename]: /java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechsynthesisvoicename