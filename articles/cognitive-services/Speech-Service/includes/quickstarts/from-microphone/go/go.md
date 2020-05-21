---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: 3740158f7134d7f0317809a7e1e89a97cdfffd94
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673004"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Criar um recurso azure speech](../../../../get-started.md)
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md)
> * Certifique-se de que tem acesso a um microfone para captura de áudio

## <a name="setup-your-environment"></a>Configurar o seu ambiente

Atualize o ficheiro go.mod com a versão Mais recente do SDK adicionando esta linha
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.12.0-alpha1
)
```

## <a name="start-with-some-boilerplate-code"></a>Comece com um pouco de código de placa de caldeira
1. Substitua o conteúdo do seu ficheiro fonte (por `sr-quickstart.go` exemplo) pelo seguinte, que inclui:

- Definição "principal" do pacote
- importação dos módulos necessários do SDK da fala
- variáveis para armazenar a informação de subscrição que será substituído mais tarde neste quickstart
- uma implementação simples usando o microfone para entrada de áudio
- manipuladores de eventos para vários eventos que ocorrem durante um reconhecimento de fala

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

Substitua os `YOUR_SUBSCRIPTION_KEY` valores e `YOUR_SUBSCRIPTIONKEY_REGION` valores por valores reais do recurso Da Fala.

- Navegue para o portal Azure e abra o recurso da Fala
- Sob as **Teclas** à esquerda, existem duas chaves de subscrição disponíveis
    - Use qualquer um como substituição de `YOUR_SUBSCRIPTION_KEY` valor
- Sob a **visão geral** à esquerda, note a região e mapeie-a para o identificador da região
- Use o identificador da Região como a substituição de `YOUR_SUBSCRIPTIONKEY_REGION` valor, por exemplo: `"westus"` para os EUA **Ocidentais**

## <a name="code-explanation"></a>Explicação de código
A chave de subscrição do Discurso e a região são necessárias para criar um objeto de configuração da fala. O objeto de configuração é necessário para instantaneamente um objeto reconhecível de fala.

A instância de reconhecimento expõe várias formas de reconhecer a fala. Neste exemplo, a fala é continuamente reconhecida. Esta funcionalidade permite que o serviço de Fala saiba que está a enviar muitas frases para reconhecimento, e quando o programa termina para deixar de reconhecer o discurso. À medida que os resultados forem obtidos, o código irá escrevê-los na consola.

## <a name="build-and-run"></a>Compilar e executar
Está agora preparado para construir o seu projeto e testar o reconhecimento da fala usando o serviço de Fala.
1. Construa o seu projeto, por exemplo, **"vai construir"**
2. Passe o módulo e diga uma frase ou frase no microfone do seu dispositivo. O seu discurso é transmitido ao serviço da Fala e transcrito para texto, que aparece na saída.


> [!NOTE]
> O SDK do Discurso não irá reconhecer o uso de en-us para a língua, consulte [especificar a linguagem fonte para a fala a texto](../../../../how-to-specify-source-language.md) para obter informações sobre a escolha da língua de origem.


## <a name="next-steps"></a>Próximos passos

[!INCLUDE [footer](./footer.md)]
