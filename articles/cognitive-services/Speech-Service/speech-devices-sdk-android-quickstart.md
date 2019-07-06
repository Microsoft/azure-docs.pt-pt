---
title: 'Início rápido: Executar os SDK de dispositivos de voz no Android - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Pré-requisitos e instruções para começar a trabalhar com um Android SDK de dispositivos de voz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 7eea978456ed565f8fc58647dc548d1a7bc76b27
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606370"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-android"></a>Início rápido: Executar a aplicação de exemplo do SDK de dispositivos de voz no Android

Neste início rápido, irá aprender como utilizar o SDK de dispositivos de voz para Android para criar um produto com funcionalidade de voz ou usá-la como uma [transcrição da conversação](conversation-transcription-service.md) dispositivo.

Este guia requer uma [serviços cognitivos do Azure](get-started.md) conta com um recurso de serviços de voz. Se não tiver uma conta, pode utilizar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de subscrição.

O código-fonte da aplicação de exemplo está incluído com o SDK de dispositivos de voz. Também vale [está disponível no GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a utilizar o SDK de dispositivos de voz, precisará:

* Siga as instruções fornecidas com o seu [kit de desenvolvimento](get-speech-devices-sdk.md) a potência do dispositivo.

* Baixe a versão mais recente dos [SDK de dispositivos de voz](https://aka.ms/sdsdk-download)e extrair o. zip para o diretório de trabalho.
   > [!NOTE]
   > O ficheiro de exemplo-Android-Release.zip inclui a aplicação de exemplo do Android e este guia de introdução pressupõe que a aplicação é extraída para C:\SDSDK\Android-Sample-Release

* Para obter um [chave de subscrição do Azure para serviços de voz](get-started.md)

* Se planeja usar o transcrição da conversação tem de utilizar um [dispositivo microfone circular](get-speech-devices-sdk.md) e esta funcionalidade só está atualmente disponível para "en-US" e "zh-CN" em regiões, "centralus" e "Sudeste Asiático". Tem de ter uma chave de voz em uma dessas regiões a utilizar a transcrição da conversação.

* Se planear utilizar os serviços de voz para identificar intenções (ou ações) de expressões de utilizador, terá uma [serviço de compreensão de idiomas (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) subscrição. Para saber mais sobre o LUIS e reconhecimento da intenção, veja [reconhecer intenções de voz com os LUIS, C# ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp).

    Pode [criar um modelo do LUIS simples](https://docs.microsoft.com/azure/cognitive-services/luis/) ou utilize o exemplo de modelo do LUIS, LUIS example.json. O exemplo de modelo do LUIS está disponível a partir da [site de download do SDK de dispositivos de voz](https://aka.ms/sdsdk-luis). Para carregar o ficheiro JSON de seu modelo para o [portal de LUIS](https://www.luis.ai/home), selecione **importar a nova aplicação**e, em seguida, selecione o ficheiro JSON.

* Instale [Android Studio](https://developer.android.com/studio/) e [Vysor](https://vysor.io/download/) no seu PC.

## <a name="set-up-the-device"></a>Configurar o dispositivo

1. Inicie Vysor no seu computador.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1. O dispositivo deve estar listado na **escolha um dispositivo**. Selecione o **vista** botão junto ao dispositivo.

1. Ligar a sua rede sem fio ao selecionar o ícone de pasta e, em seguida, selecione **configurações** > **WLAN**.

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)

    > [!NOTE]
    > Se a sua empresa tem políticas sobre como ligar dispositivos ao seu sistema de Wi-Fi, terá de obter o endereço MAC e contacte o seu departamento de TI sobre como ligá-la à. o Wi-Fi sua empresa.
    >
    > Para localizar o endereço MAC do kit de desenvolvimento, selecione o ícone de pasta do ficheiro no ambiente de trabalho do kit de desenvolvimento.
    >
    >  ![Pasta do ficheiro Vysor](media/speech-devices-sdk/qsg-10.png)
    >
    > Selecione **definições**. Procure "endereço mac" e, em seguida, selecione **endereço Mac** > **Advanced WLAN**. Anote o endereço MAC, que é apresentado junto à parte inferior da caixa de diálogo.
    >
    > ![Endereço MAC de Vysor](media/speech-devices-sdk/qsg-11.png)
    >
    > Algumas empresas podem ter um limite de tempo em quanto tempo um dispositivo pode estar ligado ao seu sistema de Wi-Fi. Poderá ter de expandir o registo do kit de desenvolvimento com o seu sistema de Wi-Fi após um número de dias específico.

## <a name="run-the-sample-application"></a>Executar o exemplo de aplicação

Validar a configuração de kit de desenvolvimento, criar e instalar a aplicação de exemplo:

1. Inicie o Android Studio.

1. Selecione **Open an existing Android Studio project** (Abrir um projeto existente do Android Studio).

   ![Android Studio - aberto um projeto existente](media/speech-devices-sdk/qsg-5.png)

1. Vá para C:\SDSDK\Android-Sample-Release\example. Selecione **OK** para abrir o projeto de exemplo.

1. Adicione a chave de subscrição de voz ao código-fonte. Se quiser experimentar o reconhecimento da intenção, adicione também seu [serviço de compreensão de idiomas](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) ID da chave de subscrição e a aplicação.

   Para conversão de voz e o LUIS, suas informações entram mainactivity. Java:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Se estiver a utilizar transcrição da conversação, suas informações de chave e a região de voz também são necessárias no conversation.java:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. A palavra de reativação de predefinição (palavra-chave) é o "Computador". Também pode tentar uma das outras fornecidas reativação palavras, como "Máquina" ou "Assistente". Os ficheiros de recursos para essas palavras de reativação alternativo são no SDK de dispositivos de voz, na pasta de palavra-chave. Por exemplo, C:\SDSDK\Android-Sample-Release\keyword\Computer contém os arquivos usados para a palavra de reativação "Computador".

   > [!TIP]
   > Também pode [criar uma palavra de reativação personalizado](speech-devices-sdk-create-kws.md).

    Para utilizar uma nova palavra de reativação, Atualize as duas linhas seguintes no `MainActivity.java`e copie o pacote de reativação do word para a sua aplicação. Por exemplo, para utilizar a palavra de reativação "Machine" de kws de pacote de word wake-machine.zip:

   * Copie o pacote de reativação do word para a pasta "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\".
   * Atualização do `MainActivity.java` com a palavra-chave e o nome do pacote:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Atualize as seguintes linhas, que contêm as definições de geometria de matriz de microfone:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   Esta tabela lista os valores suportados:

   |Variável|Significado|Valores disponíveis|
   |--------|-------|----------------|
   |`DeviceGeometry`|Configuração de mic físico|Para um kit de desenvolvimento circular: `Circular6+1` |
   |||Para um kit de programação linear: `Linear4`|
   |`SelectedGeometry`|Configuração do software do mic|Para um kit de desenvolvimento circular que utiliza todos os microfones: `Circular6+1`|
   |||Para um kit de desenvolvimento circular que utiliza quatro microfones: `Circular3+1`|
   |||Para um kit de programação linear que utiliza todos os microfones: `Linear4`|
   |||Para um kit de programação linear que utiliza dois microfones: `Linear2`|

1. Para criar a aplicação, diante da **executar** menu, selecione **executar "aplicação"** . O **selecionar destino da implementação** é apresentada a caixa de diálogo.

1. Selecione o seu dispositivo e, em seguida, selecione **OK** para implementar a aplicação no dispositivo.

    ![Selecione a caixa de diálogo de destino da implementação](media/speech-devices-sdk/qsg-7.png)

1. O aplicativo de exemplo do SDK de dispositivos de fala é iniciado e exibe as seguintes opções:

   ![Exemplo de aplicativo de exemplo de SDK de dispositivos de voz e opções](media/speech-devices-sdk/qsg-8.png)

1. Experimente a demonstração transcrição da conversação. Inicie Fotografar com "Iniciar sessão". Por predefinição, todas as pessoas são um convidado. No entanto, se tiver de assinaturas de voz de um participante podem ser colocados num arquivo `/video/participants.properties` no dispositivo. Para gerar a assinatura de voz, observe [Transcreva conversas (SDK)](how-to-use-conversation-transcription-service.md).

   ![Aplicação de transcrição da conversação de demonstração](media/speech-devices-sdk/qsg-15.png)

1. Experimentação!

## <a name="troubleshooting"></a>Resolução de problemas

   Se não conseguir ligar-se para o dispositivo de voz. Escreva o seguinte comando numa janela de linha de comando. Irá devolver uma lista de dispositivos:

   ```powershell
    adb devices
   ```

   > [!NOTE]
   > Este comando utiliza a ponte de depuração do Android, `adb.exe`, que faz parte da instalação do Android Studio. Esta ferramenta está localizada no C:\Users\[nome de utilizador] \AppData\Local\Android\Sdk\platform ferramentas. Pode adicionar esse diretório ao seu caminho para que seja mais conveniente para invocar `adb`. Caso contrário, tem de especificar o caminho completo para a instalação do adb.exe em todos os comandos que invoquem `adb`.
   >
   > Se vir um erro `no devices/emulators found` , em seguida, verificar, o cabo USB está ligado e certifique-se de que é utilizado um cabo de alta qualidade.
   >

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Reveja as notas de versão](devices-sdk-release-notes.md)
