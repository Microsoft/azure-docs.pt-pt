---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: 5e83650bc9861f982c4905e26fbb674abbd4de97
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135953"
---
Neste arranque rápido, você vai aprender a usar o SDK de Dispositivos de Fala para Android para construir um produto ativado por discurso ou usá-lo como um dispositivo [de transcrição de conversação.](../conversation-transcription-service.md)

Este guia requer uma conta [Azure Cognitive Services](../get-started.md) com um recurso de serviço de fala.

O código-fonte da aplicação da amostra está incluído no SDK dos Dispositivos de Fala. Também está [disponível no GitHub.](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a usar o SDK dos Dispositivos de Fala, terá de:

- Siga as instruções fornecidas com o seu [kit de desenvolvimento](../get-speech-devices-sdk.md) para ligar o aparelho.

- Descarregue a versão mais recente do [Speech Devices SDK](https://aka.ms/sdsdk-download)e extraia o .zip para o seu diretório de trabalho.

  > [!NOTE]
  > Este quickstart assume que a aplicação é extraída para C:\SDSDK\Android-Sample-Release

- Para obter uma [chave de subscrição Azure para o serviço speech](../get-started.md)

- Se pretender utilizar a Transcrição de Conversação, deve utilizar um [dispositivo de microfone circular](../get-speech-devices-sdk.md) e esta funcionalidade só está disponível para "en-US" e "zh-CN" em regiões, "central" e "eastasia". Deve ter uma chave de discurso numa dessas regiões para usar a Transcrição de Conversação.

- Se planeia utilizar o serviço Desemação para identificar intenções (ou ações) de declarações de utilizadores, precisará de uma subscrição do [Serviço de Compreensão de Línguas (LUIS).](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) Para saber mais sobre o LUIS e o reconhecimento de intenções, consulte [Reconhecer as intenções da fala com LUIS, C.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp).

  Pode [criar um modelo LUIS simples](https://docs.microsoft.com/azure/cognitive-services/luis/) ou utilizar o modelo LUIS de amostra, LUIS-example.js. O modelo LUIS da amostra está disponível no site de [descarregamento SDK dos Dispositivos de Fala.](https://aka.ms/sdsdk-luis) Para fazer o upload do ficheiro JSON do seu modelo para o [portal LUIS](https://www.luis.ai/home), selecione Import **new app** e, em seguida, selecione o ficheiro JSON.

- Instale [o Android Studio](https://developer.android.com/studio/) e o [Vysor](https://vysor.io/download/) no seu PC.

## <a name="set-up-the-device"></a>Configurar o dispositivo

1. Inicie o Vysor no seu computador.

   ![Vysor](../media/speech-devices-sdk/qsg-3.png)

1. O seu dispositivo deve ser listado em **Escolha um dispositivo** . Selecione o botão **Ver** ao lado do dispositivo.

1. Ligue à sua rede sem fios selecionando o ícone da pasta e, em seguida, selecione **Definições**  >  **WLAN** .

   ![Vysor WLAN](../media/speech-devices-sdk/qsg-4.png)

   > [!NOTE]
   > Se a sua empresa tiver políticas de ligação de dispositivos ao seu sistema de Wi-Fi, tem de obter o endereço MAC e contactar o seu departamento de TI sobre como ligá-lo ao Wi-Fi da sua empresa.
   >
   > Para encontrar o endereço MAC do kit dev, selecione o ícone da pasta de ficheiros no ambiente de trabalho do kit dev.
   >
   > ![Pasta de ficheiro Vysor](../media/speech-devices-sdk/qsg-10.png)
   >
   > Selecione **Definições** . Procure por "endereço mac", e, em seguida, selecione **o endereço Mac** Advanced  >  **WLAN** . Anote o endereço MAC que aparece perto da parte inferior da caixa de diálogo.
   >
   > ![Endereço Vysor MAC](../media/speech-devices-sdk/qsg-11.png)
   >
   > Algumas empresas podem ter um limite de tempo para saber quanto tempo um dispositivo pode estar ligado ao seu sistema Wi-Fi. Poderá ter de alargar o registo do dev com o seu sistema de Wi-Fi após um número específico de dias.

## <a name="run-the-sample-application"></a>Executar o exemplo de aplicação

Para validar a configuração do seu kit de desenvolvimento, construa e instale a aplicação da amostragem:

1. Inicie o Android Studio.

1. Selecione **Open an existing Android Studio project** (Abrir um projeto existente do Android Studio).

   ![Android Studio - Abra um projeto existente](../media/speech-devices-sdk/qsg-5.png)

1. Vá a C:\SDSDK\Android-Sample-Release\exemplo. Selecione **OK** para abrir o projeto exemplo.

1. Configurar gradle para referência ao Discurso SDK. Os seguintes ficheiros podem ser encontrados nos **Scripts Gradle** no Android Studio.

    Atualize o **build.gradle (Project:exemplo)** , o bloco de todos os projetos deve coincidir abaixo, adicionando as linhas maven.

    ```xml
    allprojects {
        repositories {
            google()
            jcenter()
            mavenCentral()
            maven {
                url 'https://csspeechstorage.blob.core.windows.net/maven/'
            }
        }
    }
    ```

    Atualize a **build.gradle (Módulo:app)** adicionando esta linha à secção de dependências. 
    
    ```xml
    implementation'com.microsoft.cognitiveservices.speech:client-sdk:1.14.0'
    ```
    
1. Adicione a chave de subscrição de discurso ao código fonte. Se quiser tentar o reconhecimento de intenções, adicione também a chave de subscrição do [serviço de Compreensão linguística](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) e o ID da aplicação.

   Para a fala e para o LUIS, a sua informação vai para MainActivity.java:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Se estiver a usar a transcrição da conversa, a sua chave de discurso e informação da região também são necessárias em conversa.java:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. A palavra-chave predefinida é "Computador". Também pode experimentar uma das outras palavras-chave fornecidas, como "Máquina" ou "Assistente". Os ficheiros de recursos para estas palavras-chave alternativas encontram-se na SDK dos Dispositivos de Fala, na pasta de palavras-chave. Por exemplo, C:\SDSDK\Android-Sample-Release\palavra-chave\Computer contém os ficheiros utilizados para a palavra-chave "Computador".

   > [!TIP]
   > Também pode [criar uma palavra-chave personalizada.](../speech-devices-sdk-create-kws.md)

   Para utilizar uma nova palavra-chave, atualize as duas linhas seguintes e `MainActivity.java` copie o pacote de palavras-chave para a sua aplicação. Por exemplo, utilizar a palavra-chave 'Máquina' a partir do pacote de palavras-chave kws-machine.zip:

   - Copie o pacote de palavras-chave na pasta "C:\SDSDK\Android-Sample-Release\exemplo\app\src\main\assets \" .
   - Atualize a `MainActivity.java` palavra-chave e o nome do pacote:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Atualize as seguintes linhas, que contêm as definições de geometria da matriz do microfone:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   Esta tabela lista valores suportados:

   | Variável | Significado | Valores disponíveis |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | Configuração do microfone físico | Para um kit circular dev: `Circular6+1` |
   |          |         | Para um kit linear dev: `Linear4` |
   | `SelectedGeometry` | Configuração do microfone de software | Para um kit circular dev que usa todos os microfones: `Circular6+1` |
   |          |         | Para um kit circular dev que usa quatro microfones: `Circular3+1` |
   |          |         | Para um kit linear dev que usa todos os microfones: `Linear4` |
   |          |         | Para um kit linear dev que usa dois microfones: `Linear2` |

1. Para construir a aplicação, no menu **Executar,** selecione **Executar 'app'.** Aparece a caixa de diálogo **'Destino de implementação' Select.**

1. Selecione o seu dispositivo e, em seguida, selecione **OK** para implementar a aplicação no dispositivo.

   ![Selecione a caixa de diálogo do alvo de implementação](../media/speech-devices-sdk/qsg-7.png)

1. A aplicação exemplo SDK dos Dispositivos de Fala começa e apresenta as seguintes opções:

   ![Aplicação e opções de exemplo de dispositivos de fala de amostra SDK](../media/speech-devices-sdk/qsg-8.png)

1. Experimente a nova demonstração de Transcrição de Conversação. Comece a transcrever com 'Start Session'. Por defeito, todos são convidados. No entanto, se tiver as assinaturas de voz dos participantes, podem ser colocadas num ficheiro `/video/participants.properties` no dispositivo. Para gerar a assinatura de voz, veja [as conversas transcrever (SDK)](../how-to-use-conversation-transcription-service.md).

   ![Pedido de transcrição de conversação de demonstração](../media/speech-devices-sdk/qsg-15.png)

1. Experimente!

## <a name="troubleshooting"></a>Resolução de problemas

Se não conseguir ligar-se ao Dispositivo de Fala. Digite o seguinte comando numa janela de aviso de comando. Devolverá uma lista de dispositivos:

```powershell
 adb devices
```

> [!NOTE]
> Este comando utiliza a Ponte Android Debug, `adb.exe` que faz parte da instalação do Android Studio. Esta ferramenta está localizada em C:\User \[ name]\AppData\Local\Android\Sdk\platform-tools. Pode adicionar este diretório ao seu caminho para que seja mais conveniente `adb` invocar. Caso contrário, deve especificar o caminho completo para a instalação de adb.exe em todos os comandos que `adb` invocarem .
>
> Se vir um `no devices/emulators found` erro, verifique se o cabo USB está ligado e certifique-se de que é utilizado um cabo de alta qualidade.
