---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 3a32946e10e8a81394b54fc44e4c92e8625e7ad6
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015504"
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

## <a name="text-dependent-verification"></a>Verificação dependente de texto

A Verificação do Orador é o ato de confirmar que um orador corresponde a uma voz conhecida ou **inscrita.** O primeiro passo é **inscrever** um perfil de voz, para que o serviço tenha algo para comparar futuras amostras de voz com. Neste exemplo, você inscreve o perfil usando uma estratégia dependente de texto, que requer uma **palavra-passe** específica para usar tanto para a inscrição como para a verificação. Consulte os [documentos de referência](/rest/api/speakerrecognition/) para obter uma lista de frases-passe suportadas.

Comece por [criar um perfil de voz.](/rest/api/speakerrecognition/verification/textdependent/createprofile) Terá de inserir a chave e região do serviço Speech em cada um dos comandos de caracóis neste artigo.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile":::

Note que existem três tipos de perfil de voz:

- Verificação dependente de texto
- Verificação independente de texto
- Identificação independente de texto

Neste caso, cria-se um perfil de voz de verificação dependente de texto. Deve receber a seguinte resposta.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile_response":::

Em seguida, [você matricula o perfil de voz.](/rest/api/speakerrecognition/verification/textdependent/createenrollment) Para o valor do `--data-binary` parâmetro, especifique um ficheiro áudio no seu computador que contenha uma das frases de passe suportadas, tais como "a minha voz é o meu passaporte, verifique-me". Pode gravar tal ficheiro áudio com uma aplicação como [o Windows Voice Recorder,](https://www.microsoft.com/p/windows-voice-recorder/9wzdncrfhwkn?activetab=pivot:overviewtab)ou pode gere-lo usando [texto -para-falar](../../../index-text-to-speech.yml).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll":::

Deve receber a seguinte resposta.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_1":::

Esta resposta diz-lhe que precisa de inscrever mais duas amostras de áudio.

Depois de ter matriculado um total de três amostras de áudio, deverá receber a seguinte resposta.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_2":::

Agora está pronto para [verificar uma amostra de áudio contra o perfil de voz](/rest/api/speakerrecognition/verification/textdependent/verifyprofile). Esta amostra de áudio deve conter a mesma palavra-passe que as amostras utilizadas para inscrever o perfil de voz.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify":::

Deve receber a seguinte resposta.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify_response":::

Os `Accept` meios a frase correspondeu e a verificação foi bem sucedida. A resposta também contém uma pontuação de semelhança que varia entre 0,0 e 1.0.

Para terminar, [elimine o perfil de voz](/rest/api/speakerrecognition/verification/textdependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_delete_profile":::

Não há resposta.

## <a name="text-independent-verification"></a>Verificação independente de texto

Em contraste com a verificação **dependente de texto,** verificação **independente de texto:**

* Não requer uma certa frase-passe para ser falada, qualquer coisa pode ser falada
* Não requer três amostras de áudio, mas *requer* 20 segundos de áudio total

Comece por [criar um perfil de verificação independente de texto.](/rest/api/speakerrecognition/verification/textindependent/createprofile)

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile":::

Deve receber a seguinte resposta.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile_response":::

Em seguida, [inscreva o perfil de voz.](/rest/api/speakerrecognition/verification/textindependent/createenrollment) Mais uma vez, em vez de submeter três amostras de áudio, é necessário submeter amostras de áudio que contenham um total de 20 segundos de áudio.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll":::

Depois de ter enviado amostras de áudio suficientes, deverá receber a seguinte resposta.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll_response":::

Agora está pronto para [verificar uma amostra de áudio contra o perfil de voz](/rest/api/speakerrecognition/verification/textindependent/verifyprofile). Mais uma vez, esta amostra de áudio não precisa de conter uma palavra-passe. Pode conter qualquer discurso, desde que contenha um total de pelo menos quatro segundos de áudio.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify":::

Deve receber a seguinte resposta.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify_response":::

Os `Accept` meios de verificação foram bem sucedidos. A resposta também contém uma pontuação de semelhança que varia entre 0,0 e 1.0.

Para terminar, [elimine o perfil de voz](/rest/api/speakerrecognition/verification/textindependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_delete_profile":::

Não há resposta.

## <a name="speaker-identification"></a>Identificação de orador

A identificação do altifalante é usada para determinar **quem** está a falar de um determinado grupo de vozes inscritas. O processo é semelhante à **verificação independente de texto,** com a principal diferença a ser capaz de verificar contra vários perfis de voz ao mesmo tempo, em vez de verificar com um único perfil.

Comece por [criar um perfil de identificação independente por texto.](/rest/api/speakerrecognition/identification/textindependent/createprofile)

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile":::

Deve receber a seguinte resposta.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile_response":::

Em seguida, [você matricula o perfil de voz.](/rest/api/speakerrecognition/identification/textindependent/createenrollment) Mais uma vez, precisa de enviar amostras de áudio que contenham um total de 20 segundos de áudio. Estas amostras não precisam de conter uma palavra-passe.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll":::

Depois de ter enviado amostras de áudio suficientes, deverá receber a seguinte resposta.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll_response_2":::

Agora está pronto para [identificar uma amostra de áudio utilizando o perfil de voz](/rest/api/speakerrecognition/identification/textindependent/identifysinglespeaker). O comando de identificação aceita uma lista delimitada por vírgula de possíveis identificações de perfil de voz. Neste caso, passará apenas na identificação do perfil de voz que criou anteriormente. No entanto, se quiser, pode passar em múltiplos IDs de perfil de voz onde cada perfil de voz é inscrito com amostras de áudio de uma voz diferente.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify":::

Deve receber a seguinte resposta.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify_response":::

A resposta contém o ID do perfil de voz que mais coincide com a amostra de áudio que submeteu. Contém também uma lista de perfis de voz candidatos, classificados por ordem de semelhança.

Para terminar, [elimine o perfil de voz](/rest/api/speakerrecognition/identification/textindependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_delete_profile":::

Não há resposta.