---
title: Comece com a API de reconhecimento de voz do Bing no Objective-C no iOS | Documentos da Microsoft
titlesuffix: Azure Cognitive Services
description: Utilize a API de reconhecimento de voz do Bing para desenvolver aplicações de iOS que converta áudio falado em texto.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 09b7e8961e59bd6fad49408c28e9ee9a4a209cae
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669437"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-objective-c-on-ios"></a>Início rápido: Utilizar a API de reconhecimento de voz do Bing no Objective-C no iOS

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Com a API de reconhecimento de fala, pode desenvolver aplicações de iOS que utilizam o serviço de voz com base na cloud para converter áudio falado em texto. A API suporta a transmissão em fluxo em tempo real, para que seu aplicativo possa em simultâneo e assincronamente receber resultados de reconhecimento parcial, ao mesmo tempo que seja o envio de áudio para o serviço.

Este artigo utiliza um aplicativo de exemplo para demonstrar as noções básicas de como começar com a API de reconhecimento de voz para desenvolver uma aplicação iOS. Para obter uma referência de API completa, consulte a [referência da biblioteca de cliente SDK de voz](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="platform-requirements"></a>Requisitos de plataforma

Certifique-se de que o IDE do XCode Mac está instalado.

### <a name="get-the-client-library-and-examples"></a>Instalar o cliente biblioteca e exemplos

A biblioteca de cliente de voz e exemplos para iOS que estão disponíveis na [SDK de cliente de voz para iOS](https://github.com/microsoft/cognitive-speech-stt-ios).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Subscrever a API de reconhecimento de voz e obter uma chave de subscrição de avaliação gratuita

A API de voz faz parte dos serviços cognitivos (anteriormente o projeto Oxford). Pode obter chaves de subscrição de avaliação gratuita do [subscrição dos serviços cognitivos](https://azure.microsoft.com/try/cognitive-services/) página. Depois de selecionar a API de voz, selecione **obter a chave de API** para obter a chave. Ele retorna uma chave primária e secundária. Ambas as chaves estão associadas para a mesma cota, pelo que pode utilizar qualquer uma das chaves.

Se quiser usar *reconhecimento com a intenção*, terá também de inscrever-se a [Language Understanding Intelligent Service (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
> * Obter uma chave de subscrição. Antes de poder utilizar bibliotecas de cliente de voz, tem de ter uma [chave de subscrição](https://azure.microsoft.com/try/cognitive-services/).
>
> * Utilize a sua chave de subscrição. Com o aplicativo de exemplo fornecido iOS, terá de atualizar o ficheiro Samples/SpeechRecognitionServerExample/settings.plist com a sua chave de subscrição. Para obter mais informações, consulte [compilar e executar exemplos](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Utilize a biblioteca de cliente de voz

Para adicionar a biblioteca de cliente num projeto XCode, siga estes [instruções](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-client-library).

Para encontrar o cliente de referência da biblioteca para iOS, veja este [página Web](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="build-and-run-samples"></a>Criar e executar exemplos

Para obter informações sobre como compilação e execução de exemplos, consulte esta [página Leia-me](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-sample).

## <a name="samples-explained"></a>Exemplos de explicado

### <a name="create-recognition-clients"></a>Criar clientes de reconhecimento

O código no exemplo a seguir mostra como criar classes de cliente de reconhecimento com base em cenários de usuário:

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

A biblioteca de cliente fornece reconhecimento previamente implementado classes de cliente para cenários típicos de reconhecimento de voz:

* `DataRecognitionClient`: Reconhecimento de fala com dados PCM (por exemplo, a partir de uma origem de ficheiro ou áudio). Os dados são divididos em buffers, e cada buffer é enviado para o serviço de voz. Sem modificação é feita para os buffers, para que os utilizadores podem aplicar a sua própria detecção de silêncio, se assim o desejar. Se não for fornecidos os dados de arquivos WAV, pode enviar dados do ficheiro diretamente para o servidor. Se tiver dados não processados, por exemplo, áudio através de Bluetooth, primeiro enviar um cabeçalho de formato para o servidor seguido pelos dados.
* `MicrophoneRecognitionClient`: Reconhecimento de fala com áudio proveniente de microfone. Certifique-se de que o microfone está ativado e que os dados do microfone são enviados para o serviço de reconhecimento de fala. Um "silêncio detetor" incorporada é aplicada aos dados microfone antes do envio para o serviço de reconhecimento.
* `DataRecognitionClientWithIntent` e `MicrophoneRecognitionClientWithIntent`: Para além do texto de reconhecimento, estes clientes devolvem informações estruturadas sobre a intenção do falante, que seus aplicativos podem usar para orientar mais ações. Para utilizar o "Objetivo", precisa primeiro preparar um modelo ao utilizar [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Idioma de reconhecimento

Quando utiliza `SpeechRecognitionServiceFactory` para criar o cliente, tem de selecionar um idioma. Para obter a lista completa de idiomas suportados pelo serviço de voz, consulte [idiomas suportados](../API-Reference-REST/supportedlanguages.md).

### <a name="speechrecognitionmode"></a>SpeechRecognitionMode

Também tem de especificar `SpeechRecognitionMode` ao criar o cliente com `SpeechRecognitionServiceFactory`:

* `SpeechRecognitionMode_ShortPhrase`: Uma expressão até 15 segundos de tempo. Enquanto os dados são enviados para o serviço, o cliente recebe vários resultados parciais e um resultado final com várias opções de múltipla.
* `SpeechRecognitionMode_LongDictation`: Uma expressão até dois minutos longa. Enquanto os dados são enviados para o serviço, o cliente recebe vários resultados parciais e finais, com base no qual o servidor identifica as pausas das frases.

### <a name="attach-event-handlers"></a>Anexar manipuladores de eventos

Pode anexar vários manipuladores de eventos para o cliente que criou:

* **Eventos de resultados parciais**: Este evento é chamado sempre que esse serviço de voz prevê o que poderá ser dizendo, até mesmo antes de acabar de fala (se usar `MicrophoneRecognitionClient`) ou de concluir o envio de dados (se usar `DataRecognitionClient`).
* **Eventos de erro**: Chamado quando o serviço Deteta um erro.
* **Eventos de intenção**: Chamado nos clientes de "WithIntent" (apenas no modo de ShortPhrase) após o final reconhecimento o resultado é analisado numa intenção do JSON estruturada.
* **Resultar eventos**:
  * No `SpeechRecognitionMode_ShortPhrase` modo, este evento é chamado e devolve resultados de múltipla depois de concluir a falar.
  * No `SpeechRecognitionMode_LongDictation` modo, o manipulador de eventos é chamado várias vezes, com base em onde o serviço identifica as pausas das frases.
  * **Para cada uma das opções múltipla**, são devolvidas um valor de confiança e algumas maneiras diferentes do texto reconhecido. Para obter mais informações, consulte [formato de saída](../Concepts.md#output-format).

## <a name="related-topics"></a>Tópicos relacionados

* [Referência da biblioteca de cliente para iOS](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)
* [Introdução ao reconhecimento de voz da Microsoft e/ou intenção em Java no Android](GetStartedJavaAndroid.md)
* [Comece com a API de voz da Microsoft em JavaScript](GetStartedJSWebsockets.md)
* [Comece com a API de voz de Microsoft através de REST](GetStartedREST.md)
