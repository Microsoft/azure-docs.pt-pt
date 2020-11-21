---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 489514068d83f7b2953732415ba066a2d4555df8
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015518"
---
Neste arranque rápido, você aprende padrões básicos de design para reconhecimento de alto-falante usando o SDK do discurso, incluindo:

* Verificação dependente de texto e independente de texto
* Identificação de altifalantes para identificar uma amostra de voz entre um grupo de vozes
* Eliminação de perfis de voz

Para uma visão de alto nível dos conceitos de reconhecimento de voz, consulte o artigo [de visão geral.](../../../speaker-recognition-overview.md)

## <a name="skip-to-samples-on-github"></a>Salte para as amostras no GitHub

Se quiser saltar diretamente para o código de amostra, consulte as [amostras de arranque rápido C++](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem uma conta Azure e subscrição do serviço de fala. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço Desemação](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Atualmente, o Reconhecimento de Oradores *só* é suportado nos recursos do Discurso Azure criados na `westus` região.

## <a name="install-the-speech-sdk"></a>Instale o SDK de discurso

Antes de poder fazer qualquer coisa, terá de instalar o SDK do Discurso. Dependendo da sua plataforma, utilize as seguintes instruções:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">macOS <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Janelas <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Dependências de importação

Para executar os exemplos neste artigo, adicione as seguintes declarações no topo do seu ficheiro .cpp.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="dependencies":::

## <a name="create-a-speech-configuration"></a>Criar uma configuração de discurso

Para ligar para o serviço de discurso usando o SDK de discurso, precisa de criar um [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) . Esta classe inclui informações sobre a sua subscrição, como a sua chave e região associada, ponto final, anfitrião ou token de autorização.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="get_speech_config":::

## <a name="text-dependent-verification"></a>Verificação dependente de texto

A Verificação do Orador é o ato de confirmar que um orador corresponde a uma voz conhecida ou **inscrita.** O primeiro passo é **inscrever** um perfil de voz, para que o serviço tenha algo para comparar futuras amostras de voz com. Neste exemplo, você inscreve o perfil usando uma estratégia dependente de texto, que requer uma **palavra-passe** específica para usar tanto para a inscrição como para a verificação. Consulte os [documentos de referência](/rest/api/speakerrecognition/) para obter uma lista de frases-passe suportadas.

### <a name="textdependentverification-function"></a>Função TextDependentVerification

Comece por criar a `TextDependentVerification` função.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_dependent_verification":::

Esta função cria um objeto [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) com o método [CreateProfileAsync.](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync) Note que existem três [tipos](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#enum-voiceprofiletype) `VoiceProfile` de:

- Identificação de textos
- TextDependentVerification
- TextIndependentVerification

Neste caso, `VoiceProfileType::TextDependentVerification` passa-se `CreateProfileAsync` para.

Em seguida, ligue para duas funções de ajudante que definirá a seguir, `AddEnrollmentsToTextDependentProfile` e `SpeakerVerify` . Por fim, ligue [para o DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) para limpar o perfil.

### <a name="addenrollmentstotextdependentprofile-function"></a>Função AddEnrollmentsToTextDependentProfile

Defina a seguinte função para inscrever um perfil de voz.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_dependent":::

Nesta função, você inscreve amostras de áudio em um `while` loop que rastreia o número de amostras restantes, e necessárias, para inscrição. Em cada iteração, [o EnrollProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#enrollprofileasync) pede-lhe que fale a palavra-passe no microfone e adicione a amostra ao perfil de voz.

### <a name="speakerverify-function"></a>Função SpeakerVerify

Defina `SpeakerVerify` o seguinte.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_verify":::

Nesta função, cria-se um objeto [SpeakerVerificationModel](/cpp/cognitive-services/speech/speakerverificationmodel) com o método [SpeakerVerificationModel:::FromProfile,](/cpp/cognitive-services/speech/speakerverificationmodel#fromprofile) passando no objeto [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) que criou anteriormente.

Em seguida, [SpeechRecognizer::RecogniseOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) pede-lhe que fale a palavra-passe novamente, mas desta vez irá validá-la contra o seu perfil de voz e devolver uma pontuação de semelhança que varia entre 0,0 e 1.0. O objeto [SpeakerRecognitionResult](/cpp/cognitive-services/speech/speakerrecognitionresult) também regressa `Accept` `Reject` ou, com base no facto de a palavra-passe corresponder ou não.

## <a name="text-independent-verification"></a>Verificação independente de texto

Em contraste com a verificação **dependente de texto,** verificação **independente de texto:**

* Não requer uma certa frase-passe para ser falada, qualquer coisa pode ser falada
* Não requer três amostras de áudio, mas *requer* 20 segundos de áudio total

### <a name="textindependentverification-function"></a>Função textIndependentVerification

Comece por criar a `TextIndependentVerification` função.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_verification":::

Tal como a `TextDependentVerification` função, esta função cria um objeto [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) com o método [CreateProfileAsync.](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync)

Neste caso, `VoiceProfileType::TextIndependentVerification` passa-se `CreateProfileAsync` para.

Em seguida, chama duas funções de ajudante: `AddEnrollmentsToTextIndependentProfile` que definirá a seguir, e `SpeakerVerify` que já definiu. Por fim, ligue [para o DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) para limpar o perfil.

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

Defina a seguinte função para inscrever um perfil de voz.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_independent":::

Nesta função, você inscreve amostras de áudio em um `while` loop que rastreia o número de segundos restantes, e necessário, para a inscrição. Em cada iteração, [o EnrollProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#enrollprofileasync) pede-lhe que fale para o microfone e adicione a amostra ao perfil de voz.

## <a name="speaker-identification"></a>Identificação de orador

A identificação do altifalante é usada para determinar **quem** está a falar de um determinado grupo de vozes inscritas. O processo é muito semelhante à **verificação independente de texto,** com a principal diferença a ser capaz de verificar contra vários perfis de voz ao mesmo tempo, em vez de verificar com um único perfil.

### <a name="textindependentidentification-function"></a>Função de identificação dependente de texto

Comece por criar a `TextIndependentIdentification` função.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_indentification":::

Tal como as `TextDependentVerification` `TextIndependentVerification` funções e funções, esta função cria um objeto [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) com o método [CreateProfileAsync.](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync)

Neste caso, `VoiceProfileType::TextIndependentIdentification` passa-se `CreateProfileAsync` para.

Em seguida, chama duas funções de ajudante: `AddEnrollmentsToTextIndependentProfile` que já definiu, e `SpeakerIdentify` , que definirá a seguir. Por fim, ligue [para o DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) para limpar o perfil.

### <a name="speakeridentify-function"></a>Função de identificação de altifalantes

Defina a `SpeakerIdentify` função da seguinte forma.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_identify":::

Nesta função, cria-se um objeto [SpeakerIdentificationModel](/cpp/cognitive-services/speech/speakeridentificationmodel) com o método [SpeakerIdentificationModel::FromProfiles](/cpp/cognitive-services/speech/speakeridentificationmodel#fromprofiles) method. `SpeakerIdentificationModel::FromProfiles`aceita uma lista de objetos [VoiceProfile.](/cpp/cognitive-services/speech/voiceprofile) Neste caso, passará no objeto `VoiceProfile` que criou anteriormente. No entanto, se quiser, pode passar em `VoiceProfile` vários objetos, cada um matriculado com amostras de áudio de uma voz diferente.

Em seguida, [SpeechRecognizer::RecogniseOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) pede-lhe para falar novamente. Desta vez compara a sua voz com os perfis de voz inscritos e devolve o perfil de voz mais semelhante.

## <a name="main-function"></a>Função principal

Finalmente, defina a `main` função da seguinte forma.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="main":::

Esta função simplesmente chama as funções definidas anteriormente. Primeiro, porém, cria um objeto [VoiceProfileClient](/cpp/cognitive-services/speech/voiceprofileclient) e um objeto [SpeakerRecognizer.](/cpp/cognitive-services/speech/speakerrecognizer)

```
auto speech_config = GetSpeechConfig();
auto client = VoiceProfileClient::FromConfig(speech_config);
auto recognizer = SpeakerRecognizer::FromConfig(speech_config, audio_config);
```

O `VoiceProfileClient` é usado para criar, inscrever e apagar perfis de voz. `SpeakerRecognizer`É usado para validar amostras de fala contra um ou mais perfis de voz inscritos.

## <a name="changing-audio-input-type"></a>Alteração do tipo de entrada de áudio

Os exemplos deste artigo utilizam o microfone do dispositivo predefinido como entrada para amostras de áudio. No entanto, em cenários em que é necessário utilizar ficheiros áudio em vez de entrada de microfone, basta alterar a seguinte linha:

```
auto audio_config = Audio::AudioConfig::FromDefaultMicrophoneInput();
```

para:

```
auto audio_config = Audio::AudioConfig::FromWavFileInput(path/to/your/file.wav);
```

Ou substitua qualquer utilização `audio_config` por [Audio::AudioConfig::FromWavFileInput](/cpp/cognitive-services/speech/audio-audioconfig#fromwavfileinput). Também pode ter entradas mistas, utilizando um microfone para inscrição e ficheiros para verificação, por exemplo.