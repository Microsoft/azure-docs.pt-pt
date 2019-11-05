---
title: 'Início rápido: executar o SDK de dispositivos de fala no serviço Android-Speech'
titleSuffix: Azure Cognitive Services
description: Pré-requisitos e instruções para introdução a um SDK de dispositivos de fala Android.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: acb041ce29d0340686a09764158063ad8d000c7c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491308"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-android"></a>Início rápido: executar o aplicativo de exemplo do SDK de dispositivos de fala no Android

Neste guia de início rápido, você aprenderá a usar o SDK de dispositivos de fala para Android para criar um produto habilitado para fala ou usá-lo como um dispositivo de [transcrição de conversa](conversation-transcription-service.md) .

Este guia requer uma conta de [Serviços cognitivas do Azure](get-started.md) com um recurso de serviços de fala. Se não tiver uma conta, pode utilizar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de subscrição.

O código-fonte do aplicativo de exemplo está incluído no SDK dos dispositivos de fala. Ele também está [disponível no GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a usar o SDK de dispositivos de fala, você precisará:

* Siga as instruções fornecidas com o seu [Kit de desenvolvimento](get-speech-devices-sdk.md) para ligar o dispositivo.

* Baixe a versão mais recente do [SDK dos dispositivos de fala](https://aka.ms/sdsdk-download)e extraia o. zip para seu diretório de trabalho.
   > [!NOTE]
   > O arquivo Android-Sample-Release. zip inclui o aplicativo de exemplo do Android e este início rápido pressupõe que o aplicativo é extraído para C:\SDSDK\Android-Sample-Release

* Para obter uma [chave de assinatura do Azure para serviços de fala](get-started.md)

* Se você planeja usar a transcrição de conversa, deve usar um [dispositivo de microfone circular](get-speech-devices-sdk.md) e esse recurso está disponível apenas para "en-US" e "zh-CN" em regiões, "centralus" e "eastasia". Você deve ter uma chave de fala em uma dessas regiões para usar a transcrição de conversa.

* Se você planeja usar os serviços de fala para identificar tentativas (ou ações) do usuário declarações, precisará de uma assinatura do [Luis (serviço de reconhecimento vocal)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) . Para saber mais sobre o LUIS e o reconhecimento de intenção, consulte [reconhecer tentativas de fala com C#o Luis, ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp).

    Você pode [criar um modelo Luis simples](https://docs.microsoft.com/azure/cognitive-services/luis/) ou usar o modelo Luis de exemplo, Luis-example. JSON. O modelo LUIS de exemplo está disponível no [site de download do SDK de dispositivos de fala](https://aka.ms/sdsdk-luis). Para carregar o arquivo JSON do modelo no [portal do Luis](https://www.luis.ai/home), selecione **importar novo aplicativo**e, em seguida, selecione o arquivo JSON.

* Instale [Android Studio](https://developer.android.com/studio/) e [VYSOR](https://vysor.io/download/) em seu computador.

## <a name="set-up-the-device"></a>Configurar o dispositivo

1. Inicie o Vysor no seu computador.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1. Seu dispositivo deve estar listado em **escolher um dispositivo**. Selecione o botão **Exibir** ao lado do dispositivo.

1. Conecte-se à sua rede sem fio selecionando o ícone de pasta e, em seguida, selecione **configurações** > **WLAN**.

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)

    > [!NOTE]
    > Se sua empresa tiver políticas sobre como conectar dispositivos ao seu sistema Wi-Fi, você precisará obter o endereço MAC e entrar em contato com seu departamento de ti sobre como conectá-lo ao Wi-Fi da sua empresa.
    >
    > Para localizar o endereço MAC do kit de desenvolvimento, selecione o ícone pasta de arquivo na área de trabalho do kit de desenvolvimento.
    >
    >  ![Pasta de arquivos Vysor](media/speech-devices-sdk/qsg-10.png)
    >
    > Selecione **configurações**. Pesquise por "endereço Mac" e, em seguida, selecione **endereço mac** > **WLAN avançada**. Anote o endereço MAC que aparece próximo à parte inferior da caixa de diálogo.
    >
    > ![Endereço MAC do Vysor](media/speech-devices-sdk/qsg-11.png)
    >
    > Algumas empresas podem ter um limite de tempo em que um dispositivo pode ser conectado ao seu sistema Wi-Fi. Talvez seja necessário estender o registro do kit de desenvolvimento com seu sistema Wi-Fi após um número específico de dias.

## <a name="run-the-sample-application"></a>Executar o exemplo de aplicação

Para validar a instalação do kit de desenvolvimento, crie e instale o aplicativo de exemplo:

1. Iniciar Android Studio.

1. Selecione **Open an existing Android Studio project** (Abrir um projeto existente do Android Studio).

   ![Android Studio-abrir um projeto existente](media/speech-devices-sdk/qsg-5.png)

1. Vá para C:\SDSDK\Android-Sample-Release\example. Selecione **OK** para abrir o projeto de exemplo.

1. Adicione sua chave de assinatura de fala ao código-fonte. Se você quiser tentar o reconhecimento de intenção, adicione também sua chave de assinatura de [serviço reconhecimento vocal](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) e a ID do aplicativo.

   Para fala e LUIS, suas informações vão para o MainActivity. java:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Se você estiver usando a transcrição de conversa, suas informações de chave de fala e região também serão necessárias em conversa. java:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. A palavra-chave default é "Computer". Você também pode experimentar uma das outras palavras-chave fornecidas, como "Machine" ou "Assistant". Os arquivos de recurso para essas palavras-chave alternativas estão no SDK de dispositivos de fala, na pasta de palavras-chave. Por exemplo, C:\SDSDK\Android-Sample-Release\keyword\Computer contém os arquivos usados para a palavra-chave "Computer".

   > [!TIP]
   > Você também pode [criar uma palavra-chave personalizada](speech-devices-sdk-create-kws.md).

    Para usar uma nova palavra-chave, atualize as duas linhas a seguir em `MainActivity.java`e copie o pacote de palavras-chave para seu aplicativo. Por exemplo, para usar a palavra-chave ' Machine ' do pacote de palavras-chave KWS-Machine. zip:

   * Copie o pacote de palavras-chave na pasta "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\".
   * Atualize o `MainActivity.java` com a palavra-chave e o nome do pacote:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Atualize as seguintes linhas, que contêm as configurações de geometria da matriz do microfone:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   Esta tabela lista os valores com suporte:

   |Variável|Significado|Valores disponíveis|
   |--------|-------|----------------|
   |`DeviceGeometry`|Configuração do MIC físico|Para um kit de desenvolvimento circular: `Circular6+1` |
   |||Para um kit de desenvolvimento linear: `Linear4`|
   |`SelectedGeometry`|Configuração do MIC de software|Para um kit de desenvolvimento circular que usa todos os mics: `Circular6+1`|
   |||Para um kit de desenvolvimento circular que usa quatro mics: `Circular3+1`|
   |||Para um kit de desenvolvimento linear que usa todos os mics: `Linear4`|
   |||Para um kit de desenvolvimento linear que usa dois mics: `Linear2`|

1. Para criar o aplicativo, no menu **executar** , selecione **Executar ' aplicativo '** . A caixa de diálogo **Selecionar destino de implantação** é exibida.

1. Selecione seu dispositivo e, em seguida, selecione **OK** para implantar o aplicativo no dispositivo.

    ![Caixa de diálogo Selecionar destino de implantação](media/speech-devices-sdk/qsg-7.png)

1. O aplicativo de exemplo SDK de dispositivos de fala é iniciado e exibe as seguintes opções:

   ![Exemplo de aplicativo e opções do SDK de dispositivos de fala](media/speech-devices-sdk/qsg-8.png)

1. Experimente a nova demonstração de transcrição de conversa. Comece a transcrever com ' iniciar sessão '. Por padrão, todos são convidados. No entanto, se você tiver as assinaturas de voz do participante, elas poderão ser colocadas em um arquivo `/video/participants.properties` no dispositivo. Para gerar a assinatura de voz, examine [conversações (SDK) de transcrever](how-to-use-conversation-transcription-service.md).

   ![Aplicativo de transcrição de conversa de demonstração](media/speech-devices-sdk/qsg-15.png)

1. Testes!

## <a name="troubleshooting"></a>Resolução de problemas

   Se você não puder se conectar ao dispositivo de fala. Digite o seguinte comando em uma janela de prompt de comando. Retornará uma lista de dispositivos:

   ```powershell
    adb devices
   ```

   > [!NOTE]
   > Esse comando usa o Android Debug Bridge, `adb.exe`, que faz parte da instalação do Android Studio. Essa ferramenta está localizada em C:\Users\[nome de usuário] \AppData\Local\Android\Sdk\platform-tools. Você pode adicionar esse diretório ao caminho para torná-lo mais conveniente para invocar `adb`. Caso contrário, você deve especificar o caminho completo para a instalação do ADB. exe em cada comando que invoca `adb`.
   >
   > Se você vir um erro `no devices/emulators found`, verifique se o cabo USB está conectado e se um cabo de alta qualidade é usado.
   >

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Examinar as notas de versão](devices-sdk-release-notes.md)
