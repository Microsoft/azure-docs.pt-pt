---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: 8f5a8aa2b949ddd07208cac054147596512f783f
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214342"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Criar um recurso de fala azul](../../../../overview.md#try-the-speech-service-for-free)
> * [Configurar o seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md)
> * Criar um bot ligado ao [canal Direct Line Speech](/azure/bot-service/bot-service-channel-connect-directlinespeech)
> * Certifique-se de que tem acesso a um microfone para captura de áudio
>
  > [!NOTE]
  > Por favor, consulte [a lista de regiões apoiadas para assistentes de voz](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) e certifique-se de que os seus recursos são utilizados numa dessas regiões.

## <a name="setup-your-environment"></a>Configurar o seu ambiente

Atualize o ficheiro go.mod com a versão SDK mais recente adicionando esta linha
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.15.0
)
```

## <a name="start-with-some-boilerplate-code"></a>Comece com um código de placa de caldeira
Substitua o conteúdo do seu ficheiro de origem (por `quickstart.go` exemplo) pelo seguinte, que inclui:

- Definição "principal" do pacote
- importando os módulos necessários do SDK do discurso
- variáveis para armazenar a informação bot que será substituído mais tarde neste arranque rápido
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

Substitua os `YOUR_SUBSCRIPTION_KEY` valores e `YOUR_BOT_REGION` valores reais do recurso Discurso.

- Navegue até ao portal Azure e abra o seu recurso Speech
- Em **Keys e Endpoint** à esquerda, existem duas chaves de subscrição disponíveis
    - Use qualquer um como a substituição de `YOUR_SUBSCRIPTION_KEY` valor
- Sob a **visão geral** à esquerda, observe a região e mapeeeia-a para o identificador da região
    - Utilize o identificador da Região como substituto do `YOUR_BOT_REGION` valor, por exemplo: `"westus"` para os EUA **Ocidentais**

   > [!NOTE]
   > Por favor, consulte [a lista de regiões apoiadas para assistentes de voz](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) e certifique-se de que os seus recursos são utilizados numa dessas regiões.

   > [!NOTE]
   > Para obter informações sobre a configuração do seu bot, consulte a documentação do Quadro bot para [o canal Direct Line Speech](/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="code-explanation"></a>Explicação de código
A chave e região de subscrição do Discurso são necessárias para criar um objeto de configuração da fala. O objeto de configuração é necessário para instantaneaizar um objeto de reconhecimento de fala.

O caso do reconhecedor expõe várias formas de reconhecer a fala. Neste exemplo, a fala é continuamente reconhecida. Esta funcionalidade permite ao serviço de Discurso saber que está a enviar muitas frases para reconhecimento, e quando o programa termina para deixar de reconhecer o discurso. À medida que os resultados são produzidos, o código irá escrevê-los na consola.

## <a name="build-and-run"></a>Compilar e executar
Está agora preparado para construir o seu projeto e testar o seu assistente de voz personalizado usando o serviço De Discurso.
1. Construa o seu projeto, por exemplo, **"vá construir"**
2. Executar o módulo e falar uma frase ou frase no microfone do seu dispositivo. O seu discurso é transmitido para o canal Direct Line Speech e transcrito para texto, que aparece como saída.


> [!NOTE]
> O SDK de discurso não reconhecerá a utilização do linguístico para a língua, consulte especificar a [língua de origem para falar para texto](../../../../how-to-specify-source-language.md) para obter informações sobre a escolha da língua de origem.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]