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
ms.date: 12/17/2019
ms.author: erhopf
ms.openlocfilehash: 2def0eaa2e1ee22498202228cf62257605d940e5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75380325"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-android"></a>Início rápido: executar o aplicativo de exemplo do SDK de dispositivos de fala no Android

Neste guia de início rápido, você aprenderá a usar o SDK de dispositivos de fala para Android para criar um produto habilitado para fala ou usá-lo como um dispositivo de [transcrição de conversa](conversation-transcription-service.md) .

Este guia requer uma conta de [Serviços cognitivas do Azure](get-started.md) com um recurso de serviço de fala. Se não tiver uma conta, pode utilizar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de subscrição.

O código-fonte da aplicação de exemplo está incluído com o SDK de dispositivos de voz. Também vale [está disponível no GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a usar o SDK de dispositivos de fala, você precisará:

- Siga as instruções fornecidas com o seu [Kit de desenvolvimento](get-speech-devices-sdk.md) para ligar o dispositivo.

- Baixe a versão mais recente do [SDK dos dispositivos de fala](https://aka.ms/sdsdk-download)e extraia o. zip para seu diretório de trabalho.

  > [!NOTE]
  > O arquivo Android-Sample-Release. zip inclui o aplicativo de exemplo do Android e este início rápido pressupõe que o aplicativo é extraído para C:\SDSDK\Android-Sample-Release

- Para obter uma [chave de assinatura do Azure para o serviço de fala](get-started.md)

- Se você planeja usar a transcrição de conversa, deve usar um [dispositivo de microfone circular](get-speech-devices-sdk.md) e esse recurso está disponível apenas para "en-US" e "zh-CN" em regiões, "centralus" e "eastasia". Você deve ter uma chave de fala em uma dessas regiões para usar a transcrição de conversa.

- Se você planeja usar o serviço de fala para identificar tentativas (ou ações) do usuário declarações, precisará de uma assinatura do [Luis (serviço de reconhecimento vocal)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) . Para saber mais sobre o LUIS e o reconhecimento de intenção, consulte [reconhecer tentativas de fala com C#o Luis, ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp).

  Pode [criar um modelo do LUIS simples](https://docs.microsoft.com/azure/cognitive-services/luis/) ou utilize o exemplo de modelo do LUIS, LUIS example.json. O exemplo de modelo do LUIS está disponível a partir da [site de download do SDK de dispositivos de voz](https://aka.ms/sdsdk-luis). Para carregar o ficheiro JSON de seu modelo para o [portal de LUIS](https://www.luis.ai/home), selecione **importar a nova aplicação**e, em seguida, selecione o ficheiro JSON.

- Instale [Android Studio](https://developer.android.com/studio/) e [Vysor](https://vysor.io/download/) no seu PC.

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
   > ![Pasta do ficheiro Vysor](media/speech-devices-sdk/qsg-10.png)
   >
   > Selecione **definições**. Procure "endereço mac" e, em seguida, selecione **endereço Mac** > **Advanced WLAN**. Anote o endereço MAC, que é apresentado junto à parte inferior da caixa de diálogo.
   >
   > ![Endereço MAC de Vysor](media/speech-devices-sdk/qsg-11.png)
   >
   > Algumas empresas podem ter um limite de tempo em quanto tempo um dispositivo pode estar ligado ao seu sistema de Wi-Fi. Poderá ter de expandir o registo do kit de desenvolvimento com o seu sistema de Wi-Fi após um número de dias específico.

## <a name="run-the-sample-application"></a>Executar o exemplo de aplicação

Para validar a instalação do kit de desenvolvimento, crie e instale o aplicativo de exemplo:

1. Inicie o Android Studio.

1. Selecione **Open an existing Android Studio project** (Abrir um projeto existente do Android Studio).

   ![Android Studio - aberto um projeto existente](media/speech-devices-sdk/qsg-5.png)

1. Vá para C:\SDSDK\Android-Sample-Release\example. Selecione **OK** para abrir o projeto de exemplo.

1. Adicione sua chave de assinatura de fala ao código-fonte. Se quiser experimentar o reconhecimento da intenção, adicione também seu [serviço de compreensão de idiomas](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) ID da chave de subscrição e a aplicação.

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

   - Copie o pacote de palavras-chave na pasta "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\".
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

   Esta tabela lista os valores com suporte:

   | Variável | Significado | Valores disponíveis |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | Configuração de mic físico | Para um kit de desenvolvimento circular: `Circular6+1` |
   |          |         | Para um kit de programação linear: `Linear4` |
   | `SelectedGeometry` | Configuração do software do mic | Para um kit de desenvolvimento circular que utiliza todos os microfones: `Circular6+1` |
   |          |         | Para um kit de desenvolvimento circular que utiliza quatro microfones: `Circular3+1` |
   |          |         | Para um kit de programação linear que utiliza todos os microfones: `Linear4` |
   |          |         | Para um kit de programação linear que utiliza dois microfones: `Linear2` |

1. Para criar a aplicação, diante da **executar** menu, selecione **executar "aplicação"** . O **selecionar destino da implementação** é apresentada a caixa de diálogo.

1. Selecione o seu dispositivo e, em seguida, selecione **OK** para implementar a aplicação no dispositivo.

   ![Selecione a caixa de diálogo de destino da implementação](media/speech-devices-sdk/qsg-7.png)

1. O aplicativo de exemplo do SDK de dispositivos de fala é iniciado e exibe as seguintes opções:

   ![Exemplo de aplicativo de exemplo de SDK de dispositivos de voz e opções](media/speech-devices-sdk/qsg-8.png)

1. Experimente a nova demonstração de transcrição de conversa. Comece a transcrever com ' iniciar sessão '. Por padrão, todos são convidados. No entanto, se você tiver as assinaturas de voz do participante, elas poderão ser colocadas em um arquivo `/video/participants.properties` no dispositivo. Para gerar a assinatura de voz, examine [conversações (SDK) de transcrever](how-to-use-conversation-transcription-service.md).

   ![Aplicativo de transcrição de conversa de demonstração](media/speech-devices-sdk/qsg-15.png)

1. Experimentação!

## <a name="troubleshooting"></a>Resolução de problemas

Se você não puder se conectar ao dispositivo de fala. Digite o seguinte comando em uma janela de prompt de comando. Retornará uma lista de dispositivos:

```powershell
 adb devices
```

> [!NOTE]
> Esse comando usa o Android Debug Bridge, `adb.exe`, que faz parte da instalação do Android Studio. Esta ferramenta está localizada no C:\Users\[nome de utilizador] \AppData\Local\Android\Sdk\platform ferramentas. Pode adicionar esse diretório ao seu caminho para que seja mais conveniente para invocar `adb`. Caso contrário, tem de especificar o caminho completo para a instalação do adb.exe em todos os comandos que invoquem `adb`.
>
> Se você vir um erro `no devices/emulators found`, verifique se o cabo USB está conectado e se um cabo de alta qualidade é usado.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Reveja as notas de versão](devices-sdk-release-notes.md)
