---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: a8a34a483390e12ba49123e26d2071d3aa667c89
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94424755"
---
Neste arranque rápido, você aprende padrões básicos de design para reconhecimento de alto-falante usando o SDK do discurso, incluindo:

* Verificação dependente de texto e independente de texto
* Identificação de altifalantes para identificar uma amostra de voz entre um grupo de vozes
* Eliminação de perfis de voz

Para uma visão de alto nível dos conceitos de reconhecimento de voz, consulte o artigo [de visão geral.](../../../speaker-recognition-overview.md)

## <a name="skip-to-samples-on-github"></a>Salte para as amostras no GitHub

Se quiser saltar diretamente para o código de amostra, consulte as [amostras de arranque rápido do JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/fa6428a0837779cbeae172688e0286625e340942/quickstart/javascript/node/speaker-recognition) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem uma conta Azure e subscrição do serviço de fala. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço Desemação](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Atualmente, o Reconhecimento de Oradores *só* é suportado nos recursos do Discurso Azure criados na `westus` região.

## <a name="install-the-speech-sdk"></a>Instale o SDK de discurso

Antes de poder fazer qualquer coisa, terá de instalar o <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">SDK <span class="docon docon-navigate-external x-hidden-focus"></span> </a>de voz para JavaScript . Dependendo da sua plataforma, utilize as seguintes instruções:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Navegador Web <span class="docon docon-navigate-external x-hidden-focus"></span></a>

Além disso, dependendo do ambiente-alvo, utilize um dos seguintes:

# <a name="script"></a>[script](#tab/script)

Faça o download e extrai o <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">Ficheiro SDK de discurso para <span class="docon docon-navigate-external x-hidden-focus"></span> JavaScript</a> *microsoft.cognitiveservices.speech.sdk.bundle.js* e coloque-o numa pasta acessível ao seu ficheiro HTML.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Se estiver a direcionar um navegador web e a usar a `<script>` etiqueta; o `sdk` prefixo não é necessário. O `sdk` prefixo é um pseudónimo usado para dar o nome ao `require` módulo.

# <a name="import"></a>[importar](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

Para obter mais informações `import` sobre, consulte <a href="https://javascript.info/import-export" target="_blank">a exportação e <span class="docon docon-navigate-external x-hidden-focus"></span> a importação. </a>

# <a name="require"></a>[requerer](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Para mais informações `require` sobre, veja <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">o que é necessário? <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

---

## <a name="import-dependencies"></a>Dependências de importação

Para executar os exemplos neste artigo, adicione as seguintes declarações no topo do seu ficheiro .js.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="dependencies":::

Estas declarações importam as bibliotecas necessárias e obtêm a chave de subscrição do serviço Speech e região a partir das variáveis ambientais. Também especificam caminhos para ficheiros áudio que utilizará nas seguintes tarefas.

## <a name="create-helper-function"></a>Criar função de ajudante

Adicione a seguinte função de ajudante para ler ficheiros de áudio em streams para utilização pelo serviço Speech.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="helpers":::

Nesta função, utiliza os métodos [AudioInputStream.createPushStream](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioinputstream?preserve-view=true&view=azure-node-latest#createpushstream-audiostreamformat-) e [AudioConfig.fromStreamInput](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?preserve-view=true&view=azure-node-latest#fromstreaminput-audioinputstream---pullaudioinputstreamcallback-) para criar um objeto [AudioConfig.](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?preserve-view=true&view=azure-node-latest) Este `AudioConfig` objeto representa um fluxo de áudio. Utilizará vários `AudioConfig` destes objetos durante as seguintes tarefas.

## <a name="text-dependent-verification"></a>Verificação dependente de texto

A Verificação do Orador é o ato de confirmar que um orador corresponde a uma voz conhecida ou **inscrita.** O primeiro passo é **inscrever** um perfil de voz, para que o serviço tenha algo para comparar futuras amostras de voz com. Neste exemplo, você inscreve o perfil usando uma estratégia dependente de texto, que requer uma **palavra-passe** específica para usar tanto para a inscrição como para a verificação. Consulte os [documentos de referência](/rest/api/speakerrecognition/) para obter uma lista de frases-passe suportadas.

### <a name="textdependentverification-function"></a>Função TextDependentVerification

Comece por criar a `TextDependentVerification` função.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_dependent_verification":::

Esta função cria um objeto [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?preserve-view=true&view=azure-node-latest) com o método [VoiceProfileClient.createProfileAsync.](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?preserve-view=true&view=azure-node-latest#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) Note que existem três [tipos](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofiletype?preserve-view=true&view=azure-node-latest) `VoiceProfile` de:

- Identificação de textos
- TextDependentVerification
- TextIndependentVerification

Neste caso, passa-se `VoiceProfileType.TextDependentVerification` `VoiceProfileClient.createProfileAsync` para.

Em seguida, ligue para duas funções de ajudante que definirá a seguir, `AddEnrollmentsToTextDependentProfile` e `SpeakerVerify` . Por fim, ligue para [VoiceProfileClient.deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?preserve-view=true&view=azure-node-latest#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) para remover o perfil.

### <a name="addenrollmentstotextdependentprofile-function"></a>Função AddEnrollmentsToTextDependentProfile

Defina a seguinte função para inscrever um perfil de voz.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_dependent":::

Nesta função, você chama a `GetAudioConfigFromFile` função que definiu anteriormente para criar `AudioConfig` objetos a partir de amostras de áudio. Estas amostras de áudio contêm uma frase como "A minha voz é o meu passaporte, verifica-me." Em seguida, inscreva estas amostras de áudio utilizando o método [VoiceProfileClient.enrollProfileAsync.](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?preserve-view=true&view=azure-node-latest#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-)

### <a name="speakerverify-function"></a>Função SpeakerVerify

Defina `SpeakerVerify` o seguinte.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_verify":::

Nesta função, cria-se um objeto [SpeakerVerificationModel](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel?preserve-view=true&view=azure-node-latest) com o método [SpeakerVerificationModel.FromProfile,](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel?preserve-view=true&view=azure-node-latest#fromprofile-voiceprofile-) passando no objeto [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?preserve-view=true&view=azure-node-latest) que criou anteriormente.

Em seguida, ligue para o método [SpeechRecognizer.recogniseOnceAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) para validar uma amostra de áudio que contenha a mesma frase de passe que as amostras de áudio que inscreveu anteriormente. `SpeechRecognizer.recognizeOnceAsync` retorna um objeto [SpeakerRecognitionResult,](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult?preserve-view=true&view=azure-node-latest) cuja `score` propriedade contém uma pontuação de semelhança que varia entre 0.0 e 1.0. O `SpeakerRecognitionResult` objeto também contém uma propriedade do tipo `reason` [ResultReason.](/javascript/api/microsoft-cognitiveservices-speech-sdk/resultreason?preserve-view=true&view=azure-node-latest) Se a verificação foi bem sucedida, a `reason` propriedade deve ter `RecognizedSpeaker` valor.

## <a name="text-independent-verification"></a>Verificação independente de texto

Em contraste com a verificação **dependente de texto,** verificação **independente de texto:**

* Não requer uma certa frase-passe para ser falada, qualquer coisa pode ser falada
* Não requer três amostras de áudio, mas *requer* 20 segundos de áudio total

### <a name="textindependentverification-function"></a>Função textIndependentVerification

Comece por criar a `TextIndependentVerification` função.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_verification":::

Tal como a `TextDependentVerification` função, esta função cria um objeto [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?preserve-view=true&view=azure-node-latest) com o método [VoiceProfileClient.createProfileAsync.](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?preserve-view=true&view=azure-node-latest#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-)

Neste caso, passa-se `VoiceProfileType.TextIndependentVerification` `createProfileAsync` para.

Em seguida, chama duas funções de ajudante: `AddEnrollmentsToTextIndependentProfile` que definirá a seguir, e `SpeakerVerify` que já definiu. Por fim, ligue para [VoiceProfileClient.deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?preserve-view=true&view=azure-node-latest#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) para remover o perfil.

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

Defina a seguinte função para inscrever um perfil de voz.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_independent":::

Nesta função, você chama a `GetAudioConfigFromFile` função que definiu anteriormente para criar `AudioConfig` objetos a partir de amostras de áudio. Em seguida, inscreva estas amostras de áudio utilizando o método [VoiceProfileClient.enrollProfileAsync.](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?preserve-view=true&view=azure-node-latest#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-)

## <a name="speaker-identification"></a>Identificação de orador

A identificação do altifalante é usada para determinar **quem** está a falar de um determinado grupo de vozes inscritas. O processo é semelhante à **verificação independente de texto,** com a principal diferença a ser capaz de verificar contra vários perfis de voz ao mesmo tempo, em vez de verificar com um único perfil.

### <a name="textindependentidentification-function"></a>Função de identificação dependente de texto

Comece por criar a `TextIndependentIdentification` função.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_indentification":::

Tal como as `TextDependentVerification` `TextIndependentVerification` funções, esta função cria um objeto [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?preserve-view=true&view=azure-node-latest) com o método [VoiceProfileClient.createProfileAsync.](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?preserve-view=true&view=azure-node-latest#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-)

Neste caso, passa-se `VoiceProfileType.TextIndependentIdentification` `VoiceProfileClient.createProfileAsync` para.

Em seguida, chama duas funções de ajudante: `AddEnrollmentsToTextIndependentProfile` que já definiu, e `SpeakerIdentify` , que definirá a seguir. Por fim, ligue para [VoiceProfileClient.deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?preserve-view=true&view=azure-node-latest#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) para remover o perfil.

### <a name="speakeridentify-function"></a>Função de identificação de altifalantes

Defina a `SpeakerIdentify` função da seguinte forma.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_identify":::

Nesta função, cria-se um objeto [SpeakerIdentificationModel](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel?preserve-view=true&view=azure-node-latest) com o método [SpeakerIdentificationModel.fromProfiles,](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel?preserve-view=true&view=azure-node-latest#fromprofiles-voiceprofile---) passando no objeto [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?preserve-view=true&view=azure-node-latest) que criou anteriormente.

Em seguida, ligue para o método [SpeechRecognizer.recogniseOnceAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) e passe numa amostra de áudio.
`SpeechRecognizer.recognizeOnceAsync` tenta identificar a voz desta amostra de áudio com base nos `VoiceProfile` objetos utilizados para criar o `SpeakerIdentificationModel` . Devolve um objeto [SpeakerRecognitionResult,](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult?preserve-view=true&view=azure-node-latest) cuja `profileId` propriedade identifica a `VoiceProfile` correspondência, se houver, enquanto a `score` propriedade contém uma pontuação de semelhança que varia entre 0,0 e 1.0.

## <a name="main-function"></a>Função principal

Finalmente, defina a `main` função da seguinte forma.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="main":::

Esta função cria um objeto [VoiceProfileClient,](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?preserve-view=true&view=azure-node-latest) que é usado para criar, inscrever e eliminar perfis de voz. Em seguida, chama as funções que definiu anteriormente.