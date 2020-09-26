---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: a18ce7b7c28b99967668bc33c5a94cbb58bfbc34
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376872"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Criar um recurso de discurso azul](../../../../overview.md#try-the-speech-service-for-free)
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md)
> * Certifique-se de que tem acesso a um microfone para captura de áudio

## <a name="setup-your-environment"></a>Configurar o seu ambiente

Atualize o ficheiro go.mod com a versão SDK mais recente adicionando esta linha
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.13.0
)
```

## <a name="start-with-some-boilerplate-code"></a>Comece com um código de placa de caldeira
1. Substitua o conteúdo do seu ficheiro de origem (por `sr-quickstart.go` exemplo) pelo seguinte, que inclui:

- Definição "principal" do pacote
- importando os módulos necessários do SDK do discurso
- variáveis para armazenar a informação de subscrição que será substituída mais tarde neste quickstart
- uma implementação simples usando o microfone para entrada de áudio
- manipuladores de eventos para vários eventos que ocorrem durante um reconhecimento de voz

```sh
package recognizer

import (
    "bufio"
    "fmt"
    "os"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func recognizingHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognizing:", event.Result.Text)
}

func recognizedHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognized:", event.Result.Text)
}

func cancelledHandler(event speech.SpeechRecognitionCanceledEventArgs) {
    defer event.Close()
    fmt.Println("Received a cancellation: ", event.ErrorDetails)
}

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_SUBSCRIPTIONKEY_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    speechRecognizer, err := speech.NewSpeechRecognizerFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechRecognizer.Close()
    speechRecognizer.Recognizing(recognizingHandler)
    speechRecognizer.Recognized(recognizedHandler)
    speechRecognizer.Canceled(cancelledHandler)
    speechRecognizer.StartContinuousRecognitionAsync()
    defer speechRecognizer.StopContinuousRecognitionAsync()
    bufio.NewReader(os.Stdin).ReadBytes('\n')
}
```

Substitua os `YOUR_SUBSCRIPTION_KEY` valores e `YOUR_SUBSCRIPTIONKEY_REGION` valores reais do recurso Discurso.

- Navegue até ao portal Azure e abra o recurso Speech
- Sob as **Chaves** à esquerda, existem duas chaves de subscrição disponíveis
    - Use qualquer um como a substituição de `YOUR_SUBSCRIPTION_KEY` valor
- Sob a **visão geral** à esquerda, observe a região e mapeeeia-a para o identificador da região
- Utilize o identificador da Região como substituto do `YOUR_SUBSCRIPTIONKEY_REGION` valor, por exemplo: `"westus"` para os EUA **Ocidentais**

## <a name="code-explanation"></a>Explicação de código
A chave e região de subscrição do Discurso são necessárias para criar um objeto de configuração da fala. O objeto de configuração é necessário para instantaneaizar um objeto de reconhecimento de fala.

O caso do reconhecedor expõe várias formas de reconhecer a fala. Neste exemplo, a fala é continuamente reconhecida. Esta funcionalidade permite ao serviço de Discurso saber que está a enviar muitas frases para reconhecimento, e quando o programa termina para deixar de reconhecer o discurso. À medida que os resultados são produzidos, o código irá escrevê-los na consola.

## <a name="build-and-run"></a>Compilar e executar
Está agora preparado para construir o seu projeto e testar o reconhecimento da fala usando o serviço de Fala.
1. Construa o seu projeto, por exemplo, **"vá construir"**
2. Executar o módulo e falar uma frase ou frase no microfone do seu dispositivo. O seu discurso é transmitido ao serviço de Discurso e transcrito para texto, que aparece na saída.


> [!NOTE]
> O SDK de discurso não reconhecerá a utilização do linguístico para a língua, consulte especificar a [língua de origem para falar para texto](../../../../how-to-specify-source-language.md) para obter informações sobre a escolha da língua de origem.


## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
