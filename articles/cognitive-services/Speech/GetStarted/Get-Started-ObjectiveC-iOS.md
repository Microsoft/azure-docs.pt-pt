---
title: Introdução à API de reconhecimento de Fala do Bing no Objective-C no iOS | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Use a API de reconhecimento de Fala do Bing para desenvolver aplicativos iOS que convertem áudio falado em texto.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e42784e6d2751f7e76aec8caf1d6e1f9f09a9fd1
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965938"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-objective-c-on-ios"></a>Início rápido: Usar a API de reconhecimento de Fala do Bing em Objective-C no iOS

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Com a API de reconhecimento de fala, você pode desenvolver aplicativos iOS que usam o serviço de fala baseado em nuvem para converter áudio falado em texto. A API dá suporte ao streaming em tempo real, para que seu aplicativo possa receber resultados de reconhecimento parciais de forma simultânea e assíncrona ao mesmo tempo em que está enviando áudio para o serviço.

Este artigo usa um aplicativo de exemplo para demonstrar as noções básicas de como começar a usar a API de reconhecimento de fala para desenvolver um aplicativo iOS. Para obter uma referência de API completa, consulte a [referência da biblioteca de cliente do SDK de fala](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="platform-requirements"></a>Requisitos de plataforma

Verifique se o IDE do XCode do Mac está instalado.

### <a name="get-the-client-library-and-examples"></a>Obter a biblioteca e os exemplos do cliente

O biblioteca de clientes de Fala e exemplos para iOS estão disponíveis no [SDK do cliente de fala para IOS](https://github.com/microsoft/cognitive-speech-stt-ios).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Assine a API de reconhecimento de fala e obtenha uma chave de assinatura de avaliação gratuita

O Speech API faz parte de serviços cognitivas (anteriormente Oxford de projeto). Você pode obter chaves de assinatura de avaliação gratuita da página de [assinatura de serviços cognitivas](https://azure.microsoft.com/try/cognitive-services/) . Depois de selecionar a Speech API, selecione **obter chave de API** para obter a chave. Ele retorna uma chave primária e secundária. Ambas as chaves estão vinculadas à mesma cota, para que você possa usar qualquer chave.

Se você quiser usar o *reconhecimento com o intuito*, você também precisará se inscrever no [serviço inteligente reconhecimento vocal (Luis)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
> * Obtenha uma chave de assinatura. Antes de poder usar as bibliotecas de cliente de fala, você deve ter uma [chave de assinatura](https://azure.microsoft.com/try/cognitive-services/).
>
> * Use sua chave de assinatura. Com o aplicativo de exemplo do iOS fornecido, você precisa atualizar o arquivo Samples/SpeechRecognitionServerExample/Settings. plist com sua chave de assinatura. Para obter mais informações, consulte [criar e executar exemplos](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Usar o biblioteca de clientes de Fala

Para adicionar a biblioteca de cliente em um projeto do XCode, siga estas [instruções](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-client-library).

Para localizar a referência da biblioteca de cliente para iOS, consulte esta [página da Web](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="build-and-run-samples"></a>Compilar e executar amostras

Para obter informações sobre como criar e executar exemplos, consulte esta [página de Leiame](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-sample).

## <a name="samples-explained"></a>Exemplos explicados

### <a name="create-recognition-clients"></a>Criar clientes de reconhecimento

O código a seguir no exemplo mostra como criar classes de cliente de reconhecimento com base em cenários de usuário:

```
{
    NSString* language = @"en-us";

    NSString* path = [[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"];
    NSDictionary* settings = [[NSDictionary alloc] initWithContentsOfFile:path];

    NSString* primaryOrSecondaryKey = [settings objectForKey:(@"primaryKey")];
    NSString* luisAppID = [settings objectForKey:(@"luisAppID")];
    NSString* luisSubscriptionID = [settings objectForKey:(@"luisSubscriptionID")];

    if (isMicrophoneReco) {
        if (!isIntent) {
            micClient = [SpeechRecognitionServiceFactory createMicrophoneClient:(recoMode)
                                                                   withLanguage:(language)
                                                                        withKey:(primaryOrSecondaryKey)
                                                                   withProtocol:(self)];
        }
        else {
            MicrophoneRecognitionClientWithIntent* micIntentClient;
            micIntentClient = [SpeechRecognitionServiceFactory createMicrophoneClientWithIntent:(language)
                                                                                        withKey:(primaryOrSecondaryKey)
                                                                                  withLUISAppID:(luisAppID)
                                                                                 withLUISSecret:(luisSubscriptionID)
                                                                                   withProtocol:(self)];
            micClient = micIntentClient;
        }
    }
    else {
        if (!isIntent) {
            dataClient = [SpeechRecognitionServiceFactory createDataClient:(recoMode)
                                                              withLanguage:(language)
                                                                   withKey:(primaryOrSecondaryKey)
                                                              withProtocol:(self)];
        }
        else {
            DataRecognitionClientWithIntent* dataIntentClient;
            dataIntentClient = [SpeechRecognitionServiceFactory createDataClientWithIntent:(language)
                                                                                   withKey:(primaryOrSecondaryKey)
                                                                             withLUISAppID:(luisAppID)
                                                                            withLUISSecret:(luisSubscriptionID)
                                                                              withProtocol:(self)];
            dataClient = dataIntentClient;
        }
    }
}

```

A biblioteca de cliente fornece classes de cliente de reconhecimento previamente implementadas para cenários típicos no reconhecimento de fala:

* `DataRecognitionClient`: Reconhecimento de fala com dados do PCM (por exemplo, de uma fonte de arquivo ou áudio). Os dados são divididos em buffers e cada buffer é enviado para o serviço de fala. Nenhuma modificação é feita nos buffers, para que os usuários possam aplicar sua própria detecção de silêncio, se desejado. Se os dados forem fornecidos a partir de arquivos WAV, você poderá enviar dados do direito de arquivo para o servidor. Se você tiver dados brutos, por exemplo, áudio provenientes do Bluetooth, primeiro envie um cabeçalho de formato para o servidor seguido pelos dados.
* `MicrophoneRecognitionClient`: Reconhecimento de fala com áudio proveniente do microfone. Verifique se o microfone está ligado e se os dados do microfone são enviados para o serviço de reconhecimento de fala. Um "detector de silêncio" interno é aplicado aos dados do microfone antes de ser enviado para o serviço de reconhecimento.
* `DataRecognitionClientWithIntent`e `MicrophoneRecognitionClientWithIntent`: Além do texto de reconhecimento, esses clientes retornam informações estruturadas sobre a intenção do orador, que seus aplicativos podem usar para conduzir outras ações. Para usar "intenção", você precisa primeiro treinar um modelo usando [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Idioma do reconhecimento

Ao usar `SpeechRecognitionServiceFactory` o para criar o cliente do, você deve selecionar um idioma. Para obter a lista completa de idiomas com suporte no serviço de fala, consulte [idiomas com suporte](../API-Reference-REST/supportedlanguages.md).

### <a name="speechrecognitionmode"></a>SpeechRecognitionMode

Você também precisa especificar `SpeechRecognitionMode` quando criar o cliente com: `SpeechRecognitionServiceFactory`

* `SpeechRecognitionMode_ShortPhrase`: Um expressão de até 15 segundos de comprimento. À medida que os dados são enviados para o serviço, o cliente recebe vários resultados parciais e um resultado final com várias opções n-melhores.
* `SpeechRecognitionMode_LongDictation`: Um expressão de até dois minutos de duração. À medida que os dados são enviados para o serviço, o cliente recebe vários resultados parciais e vários resultados finais, com base em onde o servidor identifica as pausas de sentença.

### <a name="attach-event-handlers"></a>Anexar manipuladores de eventos

Você pode anexar vários manipuladores de eventos ao cliente que você criou:

* **Eventos de resultados parciais**: Esse evento é chamado toda vez que o serviço de fala prevê o que você deve dizer, mesmo antes de terminar de falar (se `MicrophoneRecognitionClient`você usar) ou terminar de enviar dados ( `DataRecognitionClient`se você usar).
* **Eventos de erro**: Chamado quando o serviço detecta um erro.
* **Eventos de intenção**: Chamado em clientes "WithIntent" (somente no modo ShortPhrase) depois que o resultado do reconhecimento final é analisado em uma intenção estruturada de JSON.
* **Eventos de resultado**:
  * No `SpeechRecognitionMode_ShortPhrase` modo, esse evento é chamado e retorna n-melhores resultados depois de terminar de falar.
  * No `SpeechRecognitionMode_LongDictation` modo, o manipulador de eventos é chamado várias vezes, com base em onde o serviço identifica as pausas de sentença.
  * **Para cada uma das n melhores opções**, um valor de confiança e algumas formas diferentes do texto reconhecido são retornados. Para obter mais informações, consulte [formato de saída](../Concepts.md#output-format).

## <a name="related-topics"></a>Tópicos relacionados

* [Referência da biblioteca de cliente para iOS](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)
* [Introdução ao reconhecimento de fala da Microsoft e/ou intenção no Java no Android](GetStartedJavaAndroid.md)
* [Introdução ao Microsoft Speech API em JavaScript](GetStartedJSWebsockets.md)
* [Introdução ao Microsoft Speech API via REST](GetStartedREST.md)
