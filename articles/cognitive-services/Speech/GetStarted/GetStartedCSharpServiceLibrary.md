---
title: Introdução à API de reconhecimento de fala da Microsoft usando a C# biblioteca de serviços | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Use a biblioteca do serviço de reconhecimento de Fala do Bing para converter o idioma falado em texto.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 71c3e471a8844eb6c6b70921e40c94338a084a8b
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965845"
---
# <a name="quickstart-use-the-bing-speech-recognition-service-library-in-c35-for-net-windows"></a>Início rápido: Usar a biblioteca de serviço de reconhecimento de&#35; fala do Bing em C para .net Windows

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

A biblioteca de serviços é para os desenvolvedores que têm seu próprio serviço de nuvem e que desejam chamar o serviço de fala de seu serviço. Se você quiser chamar o serviço de reconhecimento de fala de aplicativos associados a dispositivos, não use esse SDK. (Use outras bibliotecas de cliente ou APIs REST para isso.)

Para usar a C# biblioteca de serviços, instale o [pacote NuGet Microsoft. Bing. Speech](https://www.nuget.org/packages/Microsoft.Bing.Speech/). Para obter a referência de API de biblioteca, consulte a [biblioteca do serviço de fala C# da Microsoft](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

As seções a seguir descrevem como instalar, compilar e executar o aplicativo C# de exemplo usando a biblioteca C# de serviços.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="platform-requirements"></a>Requisitos de plataforma

O exemplo a seguir foi desenvolvido para o Windows 8 + e o .NET 4.5 + Framework usando o [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Obter o aplicativo de exemplo

Clone o exemplo do repositório [de C# exemplo da biblioteca do serviço de fala](https://github.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary) .

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Assine a API de reconhecimento de fala e obtenha uma chave de assinatura de avaliação gratuita

O Speech API faz parte de serviços cognitivas (anteriormente Oxford de projeto). Você pode obter chaves de assinatura de avaliação gratuita da página de [assinatura de serviços cognitivas](https://azure.microsoft.com/try/cognitive-services/) . Depois de selecionar a Speech API, selecione **obter chave de API** para obter a chave. Ele retorna uma chave primária e secundária. Ambas as chaves estão vinculadas à mesma cota, para que você possa usar qualquer chave.

> [!IMPORTANT]
> * Obtenha uma chave de assinatura. Antes de poder usar as bibliotecas de cliente de fala, você deve ter uma [chave de assinatura](https://azure.microsoft.com/try/cognitive-services/).
>
> * Use sua chave de assinatura. Com o aplicativo C# de exemplo fornecido da biblioteca de serviços, você precisa fornecer sua chave de assinatura como um dos parâmetros de linha de comando. Para obter mais informações, consulte [executar o aplicativo de exemplo](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Passo 1: Instalar o aplicativo de exemplo

1. Inicie o Visual Studio 2015 e selecione **arquivo** > **abrir** > **projeto/solução**.

2. Clique duas vezes para abrir o arquivo de solução (. sln) do Visual Studio 2015 chamado SpeechClient. sln. A solução é aberta no Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Passo 2: Compilar o aplicativo de exemplo

Pressione Ctrl + Shift + B ou selecione **criar** no menu da faixa de opções. Em seguida, selecione **Compilar solução**.

## <a name="step-3-run-the-sample-application"></a>Passo 3: Executar o exemplo de aplicação

1. Depois que a compilação for concluída, pressione F5 ou selecione **Iniciar** no menu faixa de opções para executar o exemplo.

2. Abra o diretório de saída para o exemplo, por exemplo, SpeechClientSample\bin\Debug. Pressione SHIFT + clique com o botão direito do mouse e selecione **Abrir janela de comando aqui**.

3. Execute `SpeechClientSample.exe` com os seguintes argumentos:

   * Arg [0]: Especifique um arquivo WAV de áudio de entrada.
   * Arg [1]: Especifique a localidade do áudio.
   * Arg [2]: Especifique os modos de reconhecimento: *Curto* para o `ShortPhrase` modo e *longo* para o `LongDictation` modo.
   * Arg [3]: Especifique a chave de assinatura para acessar o serviço de reconhecimento de fala.

## <a name="samples-explained"></a>Exemplos explicados

### <a name="recognition-modes"></a>Modos de reconhecimento

* `ShortPhrase`moda Um expressão de até 15 segundos de comprimento. À medida que os dados são enviados ao servidor, o cliente recebe vários resultados parciais e um melhor resultado final.
* `LongDictation`moda Um expressão de até 10 minutos de duração. À medida que os dados são enviados para o servidor, o cliente recebe vários resultados parciais e vários resultados finais, com base em onde o servidor indica pausas de sentença.

### <a name="supported-audio-formats"></a>Formatos de áudio com suporte

O Speech API dá suporte a áudio/WAV usando os seguintes codecs:

* Canal único PCM
* Siren
* SirenSR

### <a name="preferences"></a>Preferências

Para criar um SpeechClient, você precisa primeiro criar um objeto de preferências. O objeto de preferências é um conjunto de parâmetros que configura o comportamento do serviço de fala. Ele consiste nos seguintes campos:

* `SpeechLanguage`: A localidade do áudio enviada ao serviço de fala.
* `ServiceUri`: O ponto de extremidade usado para chamar o serviço de fala.
* `AuthorizationProvider`: Uma implementação de IAuthorizationProvider usada para buscar tokens a fim de acessar o serviço de fala. Embora o exemplo forneça um provedor de autorização de serviços cognitivas, é altamente recomendável que você crie sua própria implementação para manipular o cache de token.
* `EnableAudioBuffering`: Uma opção avançada. Consulte [Gerenciamento de conexões](#connection-management).

### <a name="speech-input"></a>Entrada de fala

O objeto SpeechInput consiste em dois campos:

* **Áudio**: Uma implementação de fluxo de sua escolha da qual o SDK recebe áudio. Pode ser qualquer [fluxo](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx) que ofereça suporte à leitura.
   > [!NOTE]
   > O SDK detecta o final do fluxo quando o fluxo retorna **0** na leitura.

* **RequestMetadata**: Metadados sobre a solicitação de fala. Para obter mais informações, consulte a [referência](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

### <a name="speech-request"></a>Solicitação de fala

Depois de criar uma instância de objetos SpeechClient e SpeechInput, use RecognizeAsync para fazer uma solicitação ao serviço de fala.

```cs
    var task = speechClient.RecognizeAsync(speechInput);
```

Após a conclusão da solicitação, a tarefa retornada por RecognizeAsync é concluída. O último RecognitionResult é o fim do reconhecimento. A tarefa poderá falhar se o serviço ou o SDK falhar inesperadamente.

### <a name="speech-recognition-events"></a>Eventos de reconhecimento de fala

#### <a name="partial-results-event"></a>Evento de resultados parciais

Esse evento é chamado sempre que o serviço de fala prevê o que você deve dizer, mesmo antes de terminar de falar (se `MicrophoneRecognitionClient`você usar) ou terminar de enviar dados ( `DataRecognitionClient`se você usar). Você pode assinar o evento usando `SpeechClient.SubscribeToPartialResult()`. Ou você pode usar o método `SpeechClient.SubscribeTo<RecognitionPartialResult>()`de assinatura de eventos genéricos.

**Formato de retorno** | Descrição |
------|------
**LexicalForm** | Esse formulário é ideal para uso por aplicativos que precisam de resultados de reconhecimento de fala brutos e não processados.
**DisplayText** | A frase reconhecida com normalização inversa de texto, capitalização, pontuação e mascaramento de profanação aplicados. A profanação é mascarada com asteriscos após o caractere inicial, por exemplo, "d * * *." Esse formulário é ideal para uso por aplicativos que exibem os resultados do reconhecimento de fala para um usuário.
**Confidencial** | O nível de confiança que a frase reconhecida representa para o áudio associado, conforme definido pelo servidor de reconhecimento de fala.
**Mediatime** | A hora atual em relação ao início do fluxo de áudio (em unidades de 100 nanossegundos de tempo).
**MediaDuration** | A duração/comprimento da frase atual em relação ao segmento de áudio (em unidades de 100-nanossegundos de tempo).

#### <a name="result-event"></a>Evento de resultado
Quando você terminar de falar ( `ShortPhrase` no modo), esse evento será chamado. Você é fornecido com n-melhores opções para o resultado. No `LongDictation` modo, o evento pode ser chamado várias vezes, com base em onde o servidor indica pausas de sentença. Você pode assinar o evento usando `SpeechClient.SubscribeToRecognitionResult()`. Ou você pode usar o método `SpeechClient.SubscribeTo<RecognitionResult>()`de assinatura de eventos genéricos.

**Formato de retorno** | Descrição |
------|------|
**RecognitionStatus** | O status de como o reconhecimento foi produzido. Por exemplo, foi produzido como resultado do reconhecimento bem-sucedido ou como resultado do cancelamento da conexão, etc.
**Específicas** | O conjunto de n-melhores frases reconhecidas com a confiança de reconhecimento.

Para obter mais informações sobre os resultados de reconhecimento, consulte [formato de saída](../Concepts.md#output-format).

### <a name="speech-recognition-response"></a>Resposta de reconhecimento de fala

Exemplo de resposta de fala:
```
--- Partial result received by OnPartialResult  
---what  
--- Partial result received by OnPartialResult  
--what's  
--- Partial result received by OnPartialResult  
---what's the web  
--- Partial result received by OnPartialResult   
---what's the weather like  
---***** Phrase Recognition Status = [Success]   
***What's the weather like? (Confidence:High)  
What's the weather like? (Confidence:High)
```

## <a name="connection-management"></a>Gerenciamento de conexão

A API utiliza uma única conexão WebSocket por solicitação. Para uma experiência de usuário ideal, o SDK tenta se reconectar ao serviço de fala e iniciar o reconhecimento do último RecognitionResult recebido. Por exemplo, se a solicitação de áudio tiver dois minutos de duração, o SDK recebeu um RecognitionEvent na marca de um minuto e uma falha de rede ocorreu após cinco segundos, o SDK inicia uma nova conexão que começa da marca de um minuto.

>[!NOTE]
>O SDK não busca novamente a marca de um minuto porque o fluxo pode não dar suporte à busca. Em vez disso, o SDK mantém um buffer interno que ele usa para armazenar em buffer o áudio e limpa o buffer à medida que recebe eventos RecognitionResult.

## <a name="buffering-behavior"></a>Comportamento de buffer

Por padrão, o SDK armazena em buffer o áudio para que ele possa ser recuperado quando ocorre uma interrupção de rede. Em um cenário no qual é preferível descartar o áudio perdido durante a desconexão da rede e reiniciar a conexão, é melhor desabilitar o buffer de `EnableAudioBuffering` áudio definindo no objeto de `false`preferências como.

## <a name="related-topics"></a>Tópicos relacionados

[Referência da C# biblioteca do Microsoft Speech Service](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)
