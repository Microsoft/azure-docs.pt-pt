---
title: Comece com a API de reconhecimento de voz de Microsoft em Java no Android | Documentos da Microsoft
titlesuffix: Azure Cognitive Services
description: Utilize a API de voz da Microsoft para desenvolver aplicações Android que converta áudio falado em texto.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 147042e300e629dd7e354d4e9079cc4855a8146c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547035"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-java-on-android"></a>Início rápido: Utilizar o reconhecimento de voz do Bing API em Java no Android

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Com a API de reconhecimento de voz do Bing, pode desenvolver aplicações Android que utilizam o serviço de voz do Bing com base na cloud para converter áudio falado em texto. A API suporta a transmissão em fluxo em tempo real, para que seu aplicativo possa em simultâneo e assincronamente receber resultados de reconhecimento parcial, ao mesmo tempo que seja o envio de áudio para o serviço.

Este artigo utiliza um aplicativo de exemplo para demonstrar como utilizar a biblioteca de cliente de voz para Android para desenvolver aplicativos de voz para texto em Java para dispositivos Android.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="platform-requirements"></a>Requisitos de plataforma

O exemplo é desenvolvido pela [Android Studio](https://developer.android.com/sdk/index.html) para Windows em Java.

### <a name="get-the-client-library-and-sample-application"></a>Obter aplicação de exemplo e biblioteca de cliente

A biblioteca de cliente de voz e os exemplos para Android estão disponíveis no [o cliente de voz SDK para Android](https://github.com/microsoft/cognitive-speech-stt-android). Pode encontrar o exemplo buildable sob o diretório de exemplos/SpeechRecoExample. Pode encontrar as duas bibliotecas que precisa usar em seus próprios aplicativos no SpeechSDK/bibliotecas sob o armeabi e x86 pasta. O tamanho do ficheiro libandroid_platform.so é de 22 MB, mas será reduzido a 4 MB no momento da implementação.

#### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Subscrever a API de voz e obter uma chave de subscrição de avaliação gratuita

A API de voz faz parte dos serviços cognitivos (anteriormente o projeto Oxford). Pode obter chaves de subscrição de avaliação gratuita do [subscrição dos serviços cognitivos](https://azure.microsoft.com/try/cognitive-services/) página. Depois de selecionar a API de voz, selecione **obter a chave de API** para obter a chave. Ele retorna uma chave primária e secundária. Ambas as chaves estão associadas para a mesma cota, pelo que pode utilizar qualquer uma das chaves.

Se quiser usar *reconhecimento com a intenção*, terá também de inscrever-se a [Language Understanding Intelligent Service (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
>* Obter uma chave de subscrição. Antes de poder utilizar bibliotecas de cliente de voz, tem de ter uma [chave de subscrição](https://azure.microsoft.com/try/cognitive-services/).
>
>* Utilize a sua chave de subscrição. Com o aplicativo de exemplo fornecido do Android, atualize o ficheiro samples/SpeechRecoExample/res/values/strings.xml com as chaves de subscrição. Para obter mais informações, consulte [compilar e executar exemplos](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Utilize a biblioteca de cliente de voz

Para utilizar a biblioteca de cliente na sua aplicação, siga os [instruções](https://github.com/microsoft/cognitive-speech-stt-android#the-client-library).

Pode encontrar o cliente de referência da biblioteca para Android na pasta docs dos [o cliente de voz SDK para Android](https://github.com/microsoft/cognitive-speech-stt-android).

## <a name="build-and-run-samples"></a>Criar e executar exemplos

Para saber como criar e executar exemplos, veja este [página Leia-me](https://github.com/microsoft/cognitive-speech-stt-android#the-sample).

## <a name="samples-explained"></a>Exemplos de explicado

### <a name="create-recognition-clients"></a>Criar clientes de reconhecimento

O código no exemplo a seguir mostra como criar classes de cliente de reconhecimento com base em cenários de usuário:

```java
void initializeRecoClient()
    {
        String language = "en-us";

        String subscriptionKey = this.getString(R.string.subscription_key);
        String luisAppID = this.getString(R.string.luisAppID);
        String luisSubscriptionID = this.getString(R.string.luisSubscriptionID);

        if (m_isMicrophoneReco && null == m_micClient) {
            if (!m_isIntent) {
                m_micClient = SpeechRecognitionServiceFactory.createMicrophoneClient(this,
                                                                                     m_recoMode,
                                                                                     language,
                                                                                     this,
                                                                                     subscriptionKey);
            }
            else {
                MicrophoneRecognitionClientWithIntent intentMicClient;
                intentMicClient = SpeechRecognitionServiceFactory.createMicrophoneClientWithIntent(this,
                                                                                                   language,
                                                                                                   this,
                                                                                                   subscriptionKey,
                                                                                                   luisAppID,
                                                                                                   luisSubscriptionID);
                m_micClient = intentMicClient;

            }
        }
        else if (!m_isMicrophoneReco && null == m_dataClient) {
            if (!m_isIntent) {
                m_dataClient = SpeechRecognitionServiceFactory.createDataClient(this,
                                                                                m_recoMode,
                                                                                language,
                                                                                this,
                                                                                subscriptionKey);
            }
            else {
                DataRecognitionClientWithIntent intentDataClient;
                intentDataClient = SpeechRecognitionServiceFactory.createDataClientWithIntent(this,
                                                                                              language,
                                                                                              this,
                                                                                              subscriptionKey,
                                                                                              luisAppID,
                                                                                              luisSubscriptionID);
                m_dataClient = intentDataClient;
            }
        }
    }

```

A biblioteca de cliente fornece reconhecimento previamente implementado classes de cliente para cenários típicos de reconhecimento de voz:

* `DataRecognitionClient`: Reconhecimento de fala com dados PCM (por exemplo, a partir de uma origem de ficheiro ou áudio). Os dados são divididos em buffers, e cada buffer é enviado para o serviço de voz. Sem modificação é feita para os buffers, para que o utilizador pode aplicar a sua própria detecção de silêncio, se assim o desejar. Se não for fornecidos os dados de arquivos WAV, pode enviar dados do ficheiro diretamente para o serviço de voz. Se tiver dados não processados, por exemplo, áudio através de Bluetooth, primeiro enviar um cabeçalho de formato para o serviço de voz, seguido dos dados.
* `MicrophoneRecognitionClient`: Reconhecimento de fala com áudio proveniente de microfone. Certificar-se de que o microfone está ativado e os dados do microfone são enviados para o serviço de reconhecimento de fala. Um "silêncio detetor" incorporada é aplicada aos dados microfone antes do envio para o serviço de reconhecimento.
* `DataRecognitionClientWithIntent` e `MicrophoneRecognitionClientWithIntent`: Estes clientes voltar, além de texto de reconhecimento, informações estruturadas sobre a intenção do falante, que pode ser usado para orientar mais ações pelas suas aplicações. Para utilizar o "Objetivo", precisa primeiro preparar um modelo ao utilizar [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Idioma de reconhecimento

Quando utiliza `SpeechRecognitionServiceFactory` para criar o cliente, tem de selecionar um idioma. Para obter a lista completa de idiomas suportados pelo serviço de voz, consulte [idiomas suportados](../API-Reference-REST/supportedlanguages.md).

### `SpeechRecognitionMode`

Também tem de especificar `SpeechRecognitionMode` ao criar o cliente com `SpeechRecognitionServiceFactory`:

* `ShortPhrase`: Uma expressão até 15 segundos de tempo. Enquanto os dados são enviados para o serviço, o cliente recebe vários resultados parciais e um resultado final com várias opções de múltipla.
* `LongDictation`: Uma expressão até dois minutos longa. Enquanto os dados são enviados para o serviço, o cliente recebe vários resultados parciais e finais, com base em onde o serviço identifica as pausas das frases.

### <a name="attach-event-handlers"></a>Anexar manipuladores de eventos

Pode anexar vários manipuladores de eventos para o cliente que criou:

* **Eventos de resultados parciais**: Este evento é chamado sempre que o serviço de voz prevê o que poderá ser dizendo, até mesmo antes de acabar de fala (se usar `MicrophoneRecognitionClient`) ou de concluir o envio de dados (se usar `DataRecognitionClient`).
* **Eventos de erro**: Chamado quando o serviço Deteta um erro.
* **Eventos de intenção**: Chamado nos clientes de "WithIntent" (apenas em `ShortPhrase` modo) depois do resultado final de reconhecimento é analisado numa intenção do JSON estruturada.
* **Resultar eventos**:
  * No `ShortPhrase` modo, este evento é chamado e devolve resultados de múltipla depois de concluir a falar.
  * No `LongDictation` modo, o manipulador de eventos é chamado várias vezes, com base em onde o serviço identifica as pausas das frases.
  * **Para cada uma das opções múltipla**, são devolvidas um valor de confiança e algumas maneiras diferentes do texto reconhecido. Para obter mais informações, consulte [formato de saída](../Concepts.md#output-format).

## <a name="related-topics"></a>Tópicos relacionados

* [Referência da biblioteca de cliente para Android](https://github.com/Azure-Samples/Cognitive-Speech-STT-Android/tree/master/docs)
* [Introdução à API de voz da Microsoft em c# para Windows no .NET](GetStartedCSharpDesktop.md)
* [Comece com a API de voz de Microsoft no Objective-C no iOS](Get-Started-ObjectiveC-iOS.md)
* [Comece com a API de voz da Microsoft em JavaScript](GetStartedJSWebsockets.md)
* [Comece com a API de voz de Microsoft através de REST](GetStartedREST.md)
