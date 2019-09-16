---
title: Introdução à API de reconhecimento de fala da Microsoft em Java no Android | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Use o Speech API da Microsoft para desenvolver aplicativos Android que convertidam áudio falado em texto.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 77fee2ecee9cfabe3fad9c1df2c41c7803c3367e
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966836"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-java-on-android"></a>Início rápido: Usar a API de reconhecimento de Fala do Bing em Java no Android

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Com a API de reconhecimento de Fala do Bing, você pode desenvolver aplicativos Android que usam o serviço de Fala do Bing baseado em nuvem para converter áudio falado em texto. A API dá suporte ao streaming em tempo real, para que seu aplicativo possa receber resultados de reconhecimento parciais de forma simultânea e assíncrona ao mesmo tempo em que está enviando áudio para o serviço.

Este artigo usa um aplicativo de exemplo para demonstrar como usar o biblioteca de clientes de Fala para Android para desenvolver aplicativos de fala para texto em Java para dispositivos Android.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="platform-requirements"></a>Requisitos de plataforma

O exemplo é desenvolvido por [Android Studio](https://developer.android.com/sdk/index.html) para Windows em Java.

### <a name="get-the-client-library-and-sample-application"></a>Obter a biblioteca de cliente e o aplicativo de exemplo

Os biblioteca de clientes de Fala e exemplos para Android estão disponíveis no [SDK do cliente de fala para Android](https://github.com/microsoft/cognitive-speech-stt-android). Você pode encontrar o exemplo compilável no diretório Samples/SpeechRecoExample. Você pode encontrar as duas bibliotecas que precisa usar em seus próprios aplicativos no SpeechSDK/bibliotecas no ARMEABI e na pasta x86. O tamanho do arquivo libandroid_platform. so é 22 MB, mas é reduzido para 4 MB no momento da implantação.

#### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Assine o Speech API e obtenha uma chave de assinatura de avaliação gratuita

O Speech API faz parte de serviços cognitivas (anteriormente Oxford de projeto). Você pode obter chaves de assinatura de avaliação gratuita da página de [assinatura de serviços cognitivas](https://azure.microsoft.com/try/cognitive-services/) . Depois de selecionar a Speech API, selecione **obter chave de API** para obter a chave. Ele retorna uma chave primária e secundária. Ambas as chaves estão vinculadas à mesma cota, para que você possa usar qualquer chave.

Se você quiser usar o *reconhecimento com o intuito*, você também precisará se inscrever no [serviço inteligente reconhecimento vocal (Luis)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
>* Obtenha uma chave de assinatura. Antes de poder usar as bibliotecas de cliente de fala, você deve ter uma [chave de assinatura](https://azure.microsoft.com/try/cognitive-services/).
>
>* Use sua chave de assinatura. Com o aplicativo de exemplo do Android fornecido, atualize o arquivo Samples/SpeechRecoExample/res/Values/Strings. XML com suas chaves de assinatura. Para obter mais informações, consulte [criar e executar exemplos](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Usar o biblioteca de clientes de Fala

Para usar a biblioteca de cliente em seu aplicativo, siga as [instruções](https://github.com/microsoft/cognitive-speech-stt-android#the-client-library).

Você pode encontrar a referência de biblioteca de cliente para Android na pasta docs do [SDK do cliente de fala para Android](https://github.com/microsoft/cognitive-speech-stt-android).

## <a name="build-and-run-samples"></a>Compilar e executar amostras

Para saber como criar e executar exemplos, consulte esta [página de Leiame](https://github.com/microsoft/cognitive-speech-stt-android#the-sample).

## <a name="samples-explained"></a>Exemplos explicados

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

A biblioteca de cliente fornece classes de cliente de reconhecimento previamente implementadas para cenários típicos no reconhecimento de fala:

* `DataRecognitionClient`: Reconhecimento de fala com dados do PCM (por exemplo, de uma fonte de arquivo ou áudio). Os dados são divididos em buffers e cada buffer é enviado para o serviço de fala. Nenhuma modificação é feita nos buffers para que o usuário possa aplicar sua própria detecção de silêncio, se desejado. Se os dados forem fornecidos de arquivos WAV, você poderá enviar dados do direito de arquivo para o serviço de fala. Se você tiver dados brutos, por exemplo, áudio provenientes do Bluetooth, primeiro envie um cabeçalho de formato para o serviço de fala seguido pelos dados.
* `MicrophoneRecognitionClient`: Reconhecimento de fala com áudio proveniente do microfone. Verifique se o microfone está ligado e se os dados do microfone são enviados para o serviço de reconhecimento de fala. Um "detector de silêncio" interno é aplicado aos dados do microfone antes de ser enviado para o serviço de reconhecimento.
* `DataRecognitionClientWithIntent`e `MicrophoneRecognitionClientWithIntent`: Esses clientes retornam, além do texto de reconhecimento, informações estruturadas sobre a intenção do palestrante, que pode ser usada para gerar ações adicionais por seus aplicativos. Para usar "intenção", você precisa primeiro treinar um modelo usando [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Idioma do reconhecimento

Ao usar `SpeechRecognitionServiceFactory` o para criar o cliente do, você deve selecionar um idioma. Para obter a lista completa de idiomas com suporte no serviço de fala, consulte [idiomas com suporte](../API-Reference-REST/supportedlanguages.md).

### `SpeechRecognitionMode`

Você também precisa especificar `SpeechRecognitionMode` quando criar o cliente com: `SpeechRecognitionServiceFactory`

* `ShortPhrase`: Um expressão de até 15 segundos de comprimento. À medida que os dados são enviados para o serviço, o cliente recebe vários resultados parciais e um resultado final com várias opções n-melhores.
* `LongDictation`: Um expressão de até dois minutos de duração. À medida que os dados são enviados para o serviço, o cliente recebe vários resultados parciais e vários resultados finais, com base em onde o serviço identifica as pausas de sentença.

### <a name="attach-event-handlers"></a>Anexar manipuladores de eventos

Você pode anexar vários manipuladores de eventos ao cliente que você criou:

* **Eventos de resultados parciais**: Esse evento é chamado sempre que o serviço de fala prevê o que você deve dizer, mesmo antes de terminar de falar (se `MicrophoneRecognitionClient`você usar) ou terminar de enviar dados ( `DataRecognitionClient`se você usar).
* **Eventos de erro**: Chamado quando o serviço detecta um erro.
* **Eventos de intenção**: Chamado em clientes "WithIntent" (somente no `ShortPhrase` modo) depois que o resultado do reconhecimento final é analisado em uma intenção estruturada de JSON.
* **Eventos de resultado**:
  * No `ShortPhrase` modo, esse evento é chamado e retorna n-melhores resultados depois de terminar de falar.
  * No `LongDictation` modo, o manipulador de eventos é chamado várias vezes, com base em onde o serviço identifica as pausas de sentença.
  * **Para cada uma das n melhores opções**, um valor de confiança e algumas formas diferentes do texto reconhecido são retornados. Para obter mais informações, consulte [formato de saída](../Concepts.md#output-format).

## <a name="related-topics"></a>Tópicos relacionados

* [Referência da biblioteca de cliente para Android](https://github.com/Azure-Samples/Cognitive-Speech-STT-Android/tree/master/docs)
* [Introdução ao Microsoft Speech API no C# para Windows no .net](GetStartedCSharpDesktop.md)
* [Introdução ao Microsoft Speech API no Objective-C no iOS](Get-Started-ObjectiveC-iOS.md)
* [Introdução ao Microsoft Speech API em JavaScript](GetStartedJSWebsockets.md)
* [Introdução ao Microsoft Speech API via REST](GetStartedREST.md)
