---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: 9e41ed6bdc31ce70b2744fa9a87e10c51aaac15a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673208"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Criar um recurso de fala azure](../../../../get-started.md)
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md)
> * Crie um bot ligado ao [canal Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
> * Certifique-se de que tem acesso a um microfone para captura de áudio
>
  > [!NOTE]
  > Consulte [a lista de regiões apoiadas para assistentes](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) de voz e certifique-se de que os seus recursos são implantados numa dessas regiões.

## <a name="setup-your-environment"></a>Configurar o seu ambiente

Atualize o ficheiro go.mod com a versão Mais recente do SDK adicionando esta linha
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.12.0
)
```

## <a name="start-with-some-boilerplate-code"></a>Comece com um pouco de código de placa de caldeira
Substitua o conteúdo do seu ficheiro fonte (por `quickstart.go` exemplo) pelo seguinte, que inclui:

- Definição "principal" do pacote
- importação dos módulos necessários do SDK da fala
- variáveis para armazenar a informação bot que será substituído mais tarde neste quickstart
- uma implementação simples usando o microfone para entrada de áudio
- manipuladores de eventos para vários eventos que ocorrem durante uma interação de fala

```sh
package main

import (
    "fmt"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/dialog"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_BOT_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := dialog.NewBotFrameworkConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    connector, err := dialog.NewDialogServiceConnectorFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer connector.Close()
    activityReceivedHandler := func(event dialog.ActivityReceivedEventArgs) {
        defer event.Close()
        fmt.Println("Received an activity.")
    }
    connector.ActivityReceived(activityReceivedHandler)
    recognizedHandle := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognized ", event.Result.Text)
    }
    connector.Recognized(recognizedHandle)
    recognizingHandler := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognizing ", event.Result.Text)
    }
    connector.Recognizing(recognizingHandler)
    connector.ListenOnceAsync()
    <-time.After(10 * time.Second)
}
```

Substitua os `YOUR_SUBSCRIPTION_KEY` valores e `YOUR_BOT_REGION` valores por valores reais do recurso Da Fala.

- Navegue para o portal Azure e abra o seu recurso Speech
- Sob **Keys e Endpoint** à esquerda, existem duas chaves de subscrição disponíveis
    - Use qualquer um como substituição de `YOUR_SUBSCRIPTION_KEY` valor
- Sob a **visão geral** à esquerda, note a região e mapeie-a para o identificador da região
    - Use o identificador da Região como a substituição de `YOUR_BOT_REGION` valor, por exemplo: `"westus"` para os EUA **Ocidentais**

   > [!NOTE]
   > Consulte [a lista de regiões apoiadas para assistentes](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) de voz e certifique-se de que os seus recursos são implantados numa dessas regiões.

   > [!NOTE]
   > Para obter informações sobre a configuração do seu bot, consulte a documentação bot Framework para [o canal De Discurso da Linha Direta](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="code-explanation"></a>Explicação de código
A chave de subscrição do Discurso e a região são necessárias para criar um objeto de configuração da fala. O objeto de configuração é necessário para instantaneamente um objeto reconhecível de fala.

A instância de reconhecimento expõe várias formas de reconhecer a fala. Neste exemplo, a fala é continuamente reconhecida. Esta funcionalidade permite que o serviço de Fala saiba que está a enviar muitas frases para reconhecimento, e quando o programa termina para deixar de reconhecer o discurso. À medida que os resultados forem obtidos, o código irá escrevê-los na consola.

## <a name="build-and-run"></a>Compilar e executar
Está agora preparado para construir o seu projeto e testar o seu assistente de voz personalizado usando o serviço de Fala.
1. Construa o seu projeto, por exemplo, **"vai construir"**
2. Passe o módulo e diga uma frase ou frase no microfone do seu dispositivo. O seu discurso é transmitido para o canal Direct Line Speech e transcrito para texto, que aparece como saída.


> [!NOTE]
> O SDK do Discurso não irá reconhecer o uso de en-us para a língua, consulte [especificar a linguagem fonte para a fala a texto](../../../../how-to-specify-source-language.md) para obter informações sobre a escolha da língua de origem.

## <a name="next-steps"></a>Próximos passos

[!INCLUDE [footer](./footer.md)]
