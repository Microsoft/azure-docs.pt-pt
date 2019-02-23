---
title: Comece com a API de reconhecimento de voz de Microsoft através da biblioteca de serviço c# | Documentos da Microsoft
titlesuffix: Azure Cognitive Services
description: Utilize a biblioteca de serviço de reconhecimento de voz do Bing para converter um idioma falado em texto.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 0f445d1fff48ee7a04c0b1c1d64c808f87d824b7
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673176"
---
# <a name="quickstart-use-the-bing-speech-recognition-service-library-in-c35-for-net-windows"></a>Início rápido: Utilizar a biblioteca de serviço de reconhecimento de voz do Bing em C&#35; para Windows .NET

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

A biblioteca de serviço é para desenvolvedores que tem seu próprio serviço de nuvem e querem chamar o serviço de voz a partir do seu serviço. Se quiser chamar o serviço de reconhecimento de voz a partir de aplicações de dispositivos ligados, não utilize este SDK. (Utilizar outras bibliotecas de cliente ou REST APIs para isso.)

Para utilizar a biblioteca de serviço do c#, instalar o [pacote de NuGet Microsoft.Bing.Speech](https://www.nuget.org/packages/Microsoft.Bing.Speech/). Para a biblioteca de referência da API, consulte a [biblioteca de serviço do Microsoft fala c#](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

As secções seguintes descrevem como instalar, criar e executar a aplicação de exemplo do c# através da biblioteca de serviço do c#.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="platform-requirements"></a>Requisitos de plataforma

O exemplo a seguir foi desenvolvido para o Windows 8 e posteriores e .NET 4.5 + Framework usando [Visual Studio 2015, edição de Comunidade](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Obter a aplicação de exemplo

Clonar o exemplo a partir da [exemplo de biblioteca fala c# serviço](https://github.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary) repositório.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Subscrever a API de reconhecimento de voz e obter uma chave de subscrição de avaliação gratuita

A API de voz faz parte dos serviços cognitivos (anteriormente o projeto Oxford). Pode obter chaves de subscrição de avaliação gratuita do [subscrição dos serviços cognitivos](https://azure.microsoft.com/try/cognitive-services/) página. Depois de selecionar a API de voz, selecione **obter a chave de API** para obter a chave. Ele retorna uma chave primária e secundária. Ambas as chaves estão associadas para a mesma cota, pelo que pode utilizar qualquer uma das chaves.

> [!IMPORTANT]
> * Obter uma chave de subscrição. Antes de poder utilizar as bibliotecas de cliente de voz, tem de ter uma [chave de subscrição](https://azure.microsoft.com/try/cognitive-services/).
>
> * Utilize a sua chave de subscrição. Com o fornecido c# service biblioteca exemplo de aplicação, terá de fornecer sua chave de assinatura como um dos parâmetros da linha de comandos. Para obter mais informações, consulte [executar o aplicativo de exemplo](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Passo 1: Instalar a aplicação de exemplo

1. Inicie o Visual Studio 2015 e selecione **arquivo** > **abrir** > **projeto/solução**.

2. Faça duplo clique para abrir o ficheiro de solução de 2015 do Visual Studio (. sln) com o nome SpeechClient.sln. A solução é aberta no Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Passo 2: Criar a aplicação de exemplo

Prima Ctrl + Shift + B ou selecione **criar** no menu da faixa de opções. Em seguida, selecione **compilar solução**.

## <a name="step-3-run-the-sample-application"></a>Passo 3: Executar o exemplo de aplicação

1. Depois de concluída a compilação, prima F5 ou selecione **iniciar** no menu da faixa de opções para executar o exemplo.

2. Abra o diretório de saída para o exemplo, por exemplo, SpeechClientSample\bin\Debug. Prima Shift + direita clique e selecione **janela de comando aberto aqui**.

3. Executar `SpeechClientSample.exe` com os argumentos a seguir:

   * Arg[0]: Especifique um ficheiro de áudio WAV entrada.
   * Arg[1]: Especifique a localidade de áudio.
   * Arg[2]: Especifique os modos de reconhecimento: *Curto* para o `ShortPhrase` modo e *longo* para o `LongDictation` modo.
   * Arg[3]: Especifique a chave de subscrição para aceder ao serviço de reconhecimento de voz.

## <a name="samples-explained"></a>Exemplos de explicado

### <a name="recognition-modes"></a>Modos de reconhecimento

* `ShortPhrase` modo: Uma expressão até 15 segundos de tempo. Enquanto os dados são enviados para o servidor, o cliente recebe vários resultados parciais e um melhor resultado final.
* `LongDictation` modo: Uma expressão até 10 minutos longa. Enquanto os dados são enviados para o servidor, o cliente recebe vários resultados parciais e finais, com base em onde o servidor indica as pausas das frases.

### <a name="supported-audio-formats"></a>Formatos de áudio suportados

A API de voz oferece suporte a áudio/WAV usando os codecs seguintes:

* Canal único do PCM
* Siren
* SirenSR

### <a name="preferences"></a>Preferências

Para criar um SpeechClient, terá de criar primeiro um objeto de preferências. O objeto de preferências é um conjunto de parâmetros que configura o comportamento do serviço de voz. Ele inclui os seguintes campos:

* `SpeechLanguage`: A localidade de áudio enviado para o serviço de voz.
* `ServiceUri`: O ponto final utilizado para chamar o serviço de voz.
* `AuthorizationProvider`: Uma implementação de IAuthorizationProvider utilizada para obter tokens para poder aceder ao serviço de voz. Embora o exemplo oferece um fornecedor de autorização de serviços cognitivos, é altamente recomendável que crie sua própria implementação para lidar com tokens em cache.
* `EnableAudioBuffering`: Uma opção avançada. Ver [gerenciamento de conexões](#connection-management).

### <a name="speech-input"></a>Entrada de voz

O objeto de SpeechInput consiste em dois campos:

* **Áudio**: Uma implementação de fluxo à sua escolha a partir do qual o SDK efetua pull áudio. Ele pode ser qualquer [stream](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx) que suporta a leitura.
   > [!NOTE]
   > O SDK Deteta final da transmissão em fluxo, quando o fluxo retorna **0** na leitura.

* **RequestMetadata**: Metadados sobre o pedido de voz. Para obter mais informações, consulte a [referência](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

### <a name="speech-request"></a>Pedido de voz

Depois de ter instanciado um objetos SpeechClient e SpeechInput, utilize RecognizeAsync para fazer um pedido ao serviço de voz.

```cs
    var task = speechClient.RecognizeAsync(speechInput);
```

Depois do pedido é concluído, o task retornado pelo RecognizeAsync termina. O último RecognitionResult é o fim desse reconhecimento. A tarefa pode falhar se o serviço ou o SDK falhar inesperadamente.

### <a name="speech-recognition-events"></a>Eventos de reconhecimento de voz

#### <a name="partial-results-event"></a>Evento de resultados parciais

Este evento é chamado sempre que o serviço de voz prevê o que poderá ser dizendo, até mesmo antes de acabar de fala (se usar `MicrophoneRecognitionClient`) ou de concluir o envio de dados (se usar `DataRecognitionClient`). Assinar o evento utilizando `SpeechClient.SubscribeToPartialResult()`. Ou pode usar o método de subscrição de eventos genérico `SpeechClient.SubscribeTo<RecognitionPartialResult>()`.

**Formato de retorno** | Descrição |
------|------
**LexicalForm** | Esse formulário é ideal para utilização por aplicações que necessitam de resultados de reconhecimento de voz não processados, não processado.
**DisplayText** | A frase reconhecida com normalização do texto inversa, capitalização, pontuação e máscara de linguagem inapropriada aplicada. Linguagem inapropriada está oculto com asteriscos após o caráter inicial, por exemplo, "d ***". Esse formulário é ideal para utilização por aplicativos que exibem os resultados de reconhecimento de voz a um utilizador.
**confiança** | O nível de confiança a frase reconhecida representa para o áudio associado conforme definido pelo servidor de reconhecimento de voz.
**MediaTime** | A hora atual em relação ao início do fluxo de áudio (em unidades de 100 nanossegundos de tempo).
**MediaDuration** | O frase duração/comprimento atual em relação ao segmento de áudio (em unidades de 100 nanossegundos de tempo).

#### <a name="result-event"></a>Evento de resultado
Quando terminar de fala (no `ShortPhrase` modo), este evento é chamado. É-lhe fornecido múltipla de opções para o resultado. No `LongDictation` modo, o evento pode ser chamado várias vezes, com base em onde o servidor indica as pausas das frases. Assinar o evento utilizando `SpeechClient.SubscribeToRecognitionResult()`. Ou pode usar o método de subscrição de eventos genérico `SpeechClient.SubscribeTo<RecognitionResult>()`.

**Formato de retorno** | Descrição |
------|------|
**RecognitionStatus** | O estado em como o reconhecimento foi produzido. Por exemplo, foi ele produzido como resultado do reconhecimento bem-sucedido ou como resultado a cancelar a ligação, etc.
**Frases** | O conjunto de múltipla frases reconhecidos com a confiança de reconhecimento.

Para obter mais informações sobre os resultados de reconhecimento, consulte [formato de saída](../Concepts.md#output-format).

### <a name="speech-recognition-response"></a>Resposta de reconhecimento de voz

Exemplo de resposta de voz:
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

## <a name="connection-management"></a>Gerenciamento de conexões

A API utiliza uma conexão WebSocket única por solicitação. Experiência de usuário ideal, o SDK tenta restabelecer ligação ao serviço de voz e iniciar o reconhecimento a partir do último RecognitionResult recebida. Por exemplo, se o pedido de áudio é dois minutos de duração, o SDK recebeu um RecognitionEvent na marca de um minuto e Ocorreu uma falha de rede após cinco segundos, o SDK inicia uma nova conexão que começa com a marca de um minuto.

>[!NOTE]
>O SDK não procurar o ressarcimento de volta na marca de um minuto porque o fluxo não suportem a procurar. Em vez disso, o SDK mantém um buffer interno que utiliza para o áudio de buffer e limpa a memória intermédia, à medida que ele recebe eventos RecognitionResult.

## <a name="buffering-behavior"></a>Comportamento da memória intermédia

Por predefinição, o SDK coloca na memória intermédia áudio, para que ele pode recuperar quando ocorre uma interrupção de rede. Num cenário em que é preferível para eliminar o áudio perdido ao desligar a rede e reinicie a ligação, é melhor desativar a memória intermédia áudio definindo `EnableAudioBuffering` no objeto preferências para `false`.

## <a name="related-topics"></a>Tópicos relacionados

[Referência da biblioteca Microsoft fala c# serviço](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)
