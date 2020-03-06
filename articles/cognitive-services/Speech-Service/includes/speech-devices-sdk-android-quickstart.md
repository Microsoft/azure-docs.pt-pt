---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: dapine
ms.openlocfilehash: 2ee6b12923bfd0e06343e8f185226cb72280d806
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383826"
---
Neste arranque rápido, aprenderá a usar o SDK de Dispositivos de Fala para Android para construir um produto ativado pela fala ou usá-lo como um dispositivo de Transcrição de [Conversas.](../conversation-transcription-service.md)

Este guia requer uma conta [de Serviços Cognitivos Azure](../get-started.md) com um recurso de serviço da Fala. Se não tiver uma conta, pode utilizar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de subscrição.

O código-fonte da aplicação de exemplo está incluído com o SDK de dispositivos de voz. Também está [disponível no GitHub.](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a usar o SDK dos Dispositivos de Fala, terá de:

- Siga as instruções fornecidas com o seu kit de [desenvolvimento](../get-speech-devices-sdk.md) para ligar o dispositivo.

- Descarregue a versão mais recente do [SDK](https://aka.ms/sdsdk-download)dos Dispositivos de Fala e extraa o .zip para o seu diretório de trabalho.

  > [!NOTE]
  > Este quickstart assume que a aplicação é extraída para C:\SDSDK\Android-Sample-Release

- Para obter uma [chave de subscrição Azure para o serviço de Fala](../get-started.md)

- Se planeia utilizar a Transcrição de Conversas, deve utilizar um dispositivo de [microfone circular](../get-speech-devices-sdk.md) e esta funcionalidade está atualmente disponível apenas para "en-US" e "zh-CN" em regiões, "centralus" e "eastasia". Deve ter uma chave de discurso numa dessas regiões para usar a Transcrição de Conversações.

- Se planeia utilizar o serviço de Fala para identificar intenções (ou ações) de declarações de utilizadores, necessitará de uma subscrição do Serviço de Compreensão de [Línguas (LUIS).](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) Para saber mais sobre LUIS e reconhecimento de intenções, consulte [Reconhecer as intenções de fala com C#LUIS, ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp).

  Pode [criar um modelo LUIS simples](https://docs.microsoft.com/azure/cognitive-services/luis/) ou utilizar o modelo LUIS da amostra, LUIS-exemplo.json. O modelo LUIS da amostra está disponível no site de [descarregamento de Dispositivos de Fala SDK](https://aka.ms/sdsdk-luis). Para fazer o upload do ficheiro JSON do seu modelo para o [portal LUIS,](https://www.luis.ai/home)selecione **importar nova aplicação**e, em seguida, selecione o ficheiro JSON.

- Instale [o Android Studio](https://developer.android.com/studio/) e o [Vysor](https://vysor.io/download/) no seu PC.

## <a name="set-up-the-device"></a>Configurar o dispositivo

1. Inicie Vysor no seu computador.

   ![Vysor](../media/speech-devices-sdk/qsg-3.png)

1. O seu dispositivo deve ser listado em **Escolher um dispositivo**. Selecione o botão **'Ver'** ao lado do dispositivo.

1. Ligue à sua rede sem fios selecionando o ícone da pasta e, em seguida, selecione **Definições** > **WLAN**.

   ![Vysor WLAN](../media/speech-devices-sdk/qsg-4.png)

   > [!NOTE]
   > Se a sua empresa tem políticas sobre como ligar dispositivos ao seu sistema de Wi-Fi, terá de obter o endereço MAC e contacte o seu departamento de TI sobre como ligá-la à. o Wi-Fi sua empresa.
   >
   > Para localizar o endereço MAC do kit de desenvolvimento, selecione o ícone de pasta do ficheiro no ambiente de trabalho do kit de desenvolvimento.
   >
   > ![Pasta do ficheiro Vysor](../media/speech-devices-sdk/qsg-10.png)
   >
   > Selecione **Definições**. Procure por "mac address", e, em seguida, selecione **o endereço Mac** > Advanced **WLAN**. Anote o endereço MAC, que é apresentado junto à parte inferior da caixa de diálogo.
   >
   > ![Endereço MAC de Vysor](../media/speech-devices-sdk/qsg-11.png)
   >
   > Algumas empresas podem ter um limite de tempo em quanto tempo um dispositivo pode estar ligado ao seu sistema de Wi-Fi. Poderá ter de expandir o registo do kit de desenvolvimento com o seu sistema de Wi-Fi após um número de dias específico.

## <a name="run-the-sample-application"></a>Executar o exemplo de aplicação

Para validar a configuração do seu kit de desenvolvimento, construir e instalar a aplicação da amostra:

1. Inicie o Android Studio.

1. Selecione **Open an existing Android Studio project** (Abrir um projeto existente do Android Studio).

   ![Android Studio - aberto um projeto existente](../media/speech-devices-sdk/qsg-5.png)

1. Vá para C:\SDSDK\Android-Sample-Release\example. Selecione **OK** para abrir o projeto de exemplo.

1. Configure o gradle para fazer referência ao SDK da Fala. Os seguintes ficheiros podem ser encontrados em **Scripts Gradle** no Android Studio.

    Atualize o **build.gradle (Project:example)** , o bloco de todos os projetos deve coincidir abaixo, adicionando as linhas maven.

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

    Atualize o **build.gradle (Module:app)** adicionando esta linha à secção de dependências. 
    
    ```xml
    implementation'com.microsoft.cognitiveservices.speech:client-sdk:1.10.0'
    ```
    
1. Adicione a chave de subscrição do seu discurso ao código fonte. Se quiser experimentar o reconhecimento de intenções, adicione também a chave de subscrição do [serviço De compreensão](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) de idiomas e o ID da aplicação.

   Para a fala e para o LUIS, a sua informação vai para mainActivity.java:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Se estiver a usar a transcrição da conversação, a sua chave de fala e informações da região também são necessárias em conversação.java:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. A palavra-chave padrão é "Computador". Também pode experimentar uma das outras palavras-chave fornecidas, como "Máquina" ou "Assistente". Os ficheiros de recursos para estas palavras-chave alternativas estão no SDK dos Dispositivos de Fala, na pasta das palavras-chave. Por exemplo, C:\SDSDK\Android-Data de Lançamento\palavra-chave\Computer contém os ficheiros utilizados para a palavra-chave "Computador".

   > [!TIP]
   > Também pode [criar uma palavra-chave personalizada.](../speech-devices-sdk-create-kws.md)

   Para utilizar uma nova palavra-chave, atualize as duas linhas seguintes em `MainActivity.java`e copie o pacote de palavras-chave para a sua aplicação. Por exemplo, utilizar a palavra-chave 'Máquina' do pacote de palavras-chave kws-machine.zip:

   - Copie o pacote de palavras-chave na pasta "C:\SDSDK\Android-Sample-Release\exemplo\app\src\main\assets\".
   - Atualize o `MainActivity.java` com a palavra-chave e o nome do pacote:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Atualize as seguintes linhas, que contêm as definições de geometria de matriz de microfone:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   Esta tabela enumera valores suportados:

   | Variável | Significado | Valores disponíveis |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | Configuração de mic físico | Para um kit de dev circular: `Circular6+1` |
   |          |         | Para um kit de dev linear: `Linear4` |
   | `SelectedGeometry` | Configuração do software do mic | Para um kit de dev circular que usa todos os microfones: `Circular6+1` |
   |          |         | Para um kit de dev circular que usa quatro microfones: `Circular3+1` |
   |          |         | Para um kit de v linear que usa todos os microfones: `Linear4` |
   |          |         | Para um kit de dev linear que usa dois microfones: `Linear2` |

1. Para construir a aplicação, no menu **Executar,** selecione **Executar 'app'** . Aparece a caixa de diálogo **Select Deployment Target.**

1. Selecione o seu dispositivo e, em seguida, selecione **OK** para implementar a aplicação no dispositivo.

   ![Selecione a caixa de diálogo de destino da implementação](../media/speech-devices-sdk/qsg-7.png)

1. O aplicativo de exemplo do SDK de dispositivos de fala é iniciado e exibe as seguintes opções:

   ![Exemplo de aplicativo de exemplo de SDK de dispositivos de voz e opções](../media/speech-devices-sdk/qsg-8.png)

1. Experimente a nova demonstração de Transcrição de Conversas. Comece a transcrever com 'Start Session'. Por defeito, todos são convidados. No entanto, se tiver as assinaturas de voz dos participantes, podem ser colocadas num ficheiro `/video/participants.properties` no dispositivo. Para gerar a assinatura de voz, veja as [conversas do Transcribe (SDK)](../how-to-use-conversation-transcription-service.md).

   ![Aplicação de transcrição de conversação de demonstração](../media/speech-devices-sdk/qsg-15.png)

1. Experimentação!

## <a name="troubleshooting"></a>Resolução de problemas

Se não conseguir ligar-se ao Dispositivo da Fala. Digite o seguinte comando numa janela De Comando. Devolverá uma lista de dispositivos:

```powershell
 adb devices
```

> [!NOTE]
> Este comando utiliza a Ponte Android Debug, `adb.exe`, que faz parte da instalação do Android Studio. Esta ferramenta está localizada em C:\Users\[nome de utilizador]\AppData\Local\Android\Sdk\platform-tools. Pode adicionar este diretório ao seu caminho para que seja mais conveniente invocar `adb`. Caso contrário, deve especificar o caminho completo para a sua instalação de adb.exe em todos os comandos que invoquem `adb`.
>
> Se vir um erro `no devices/emulators found`, verifique se o cabo USB está ligado e certifique-se de que um cabo de alta qualidade é utilizado.
