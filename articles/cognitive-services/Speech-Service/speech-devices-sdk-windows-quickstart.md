---
title: 'Início rápido: executar o SDK de dispositivos de fala no Windows'
titleSuffix: Azure Cognitive Services
description: Pré-requisitos e instruções para introdução a um SDK de dispositivos de fala do Windows.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/13/2019
ms.author: erhopf
ms.openlocfilehash: e4da99d895ba7a6d9ce537ab513ce4cc248aff7a
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111679"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-windows"></a>Início rápido: executar o aplicativo de exemplo do SDK de dispositivos de fala no Windows

Neste guia de início rápido, você aprenderá a usar o SDK de dispositivos de fala para Windows para criar um produto habilitado para fala ou usá-lo como um dispositivo de [transcrição de conversa](conversation-transcription-service.md) . Para a transcrição de conversa, somente o [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) tem suporte. Para outra fala, use matrizes MIC lineares que fornecem uma geometria de matriz de microfone com suporte.

O aplicativo é criado com o pacote do SDK de fala e o Java IDE do Eclipse (v4) no Windows de 64 bits. É executada num ambiente de tempo de execução Java 8 de 64 bits (JRE).

Este guia requer uma conta de [Serviços cognitivas do Azure](get-started.md) com um recurso de serviços de fala. Se não tiver uma conta, pode utilizar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de subscrição.

O código-fonte do [aplicativo de exemplo](https://aka.ms/sdsdk-download-JRE) está incluído no SDK dos dispositivos de fala. Também vale [está disponível no GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Sistema operacional: Windows de 64 bits
* Uma matriz de microfone, como o [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Java IDE Eclipse](https://www.eclipse.org/downloads/)
* Somente [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) .
* [Microsoft Visual C++ Redistributable](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)
* Uma chave de assinatura do Azure para o serviço de fala. [Obtenha um gratuitamente](get-started.md).
* Baixe a versão mais recente do [SDK de dispositivos de fala](https://aka.ms/sdsdk-download-JRE) para Java e extraia o. zip para seu diretório de trabalho.
   > [!NOTE]
   > O arquivo JRE-Sample-Release. zip inclui o aplicativo de exemplo do JRE e este guia de início rápido pressupõe que o aplicativo é extraído para C:\SDSDK\JRE-Sample-Release

Atualmente, a transcrição de conversa está disponível apenas para "en-US" e "zh-CN", nas regiões "centralus" e "eastasia". Você deve ter uma chave de fala em uma dessas regiões para usar a transcrição de conversa.

Se você planeja usar as intenções, precisará de uma assinatura do [Luis (serviço de reconhecimento vocal)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) . Para saber mais sobre o LUIS e o reconhecimento de intenção, consulte [reconhecer tentativas de fala com C#o Luis, ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). Um [modelo Luis de exemplo](https://aka.ms/sdsdk-luis) está disponível para este aplicativo.

## <a name="create-and-configure-the-project"></a>Criar e configurar o projeto

1. Inicie o Eclipse.

1. No **iniciador do IDE do eclipse**, no campo **espaço de trabalho** , digite o nome de um novo diretório de espaço de trabalho. Em seguida, selecione **Iniciar**.

   ![Captura de ecrã do Iniciador do Eclipse](media/speech-devices-sdk/eclipse-launcher.png)

1. Dentro de momentos, é apresentada a janela principal do IDE do Eclipse. Feche o ecrã de Boas-Vindas, se existir.

1. Na barra de menus do eclipse, crie um novo projeto escolhendo **arquivo** > **novo** > **projeto Java**. Se não estiver disponível, escolha **projeto** e, em seguida, **projeto Java**.

1. O assistente de **novo projeto Java** é iniciado. **Procure** o local do projeto de exemplo. Selecione **Concluir**.

   ![Captura de ecrã do assistente Novo Projeto Java](media/speech-devices-sdk/eclipse-new-java-project.png)

1. No **Explorador de pacotes**, clique com o botão direito do mouse em seu projeto. Escolha **Configurar** > **Converter em Projeto Maven** no menu de contexto. Selecione **Concluir**.

   ![Captura de ecrã do Explorador de pacotes](media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Abra o arquivo pom. xml e edite-o.

    No final do arquivo, antes da marca de fechamento `</project>`, crie `repositories` e `dependencies` elementos, como mostrado aqui, e verifique se o `version` corresponde à sua versão atual:
    ```xml    
    <repositories>
         <repository>
             <id>maven-cognitiveservices-speech</id>
             <name>Microsoft Cognitive Services Speech Maven Repository</name>
             <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
         </repository>
    </repositories>
 
    <dependencies>
        <dependency>
             <groupId>com.microsoft.cognitiveservices.speech</groupId>
             <artifactId>client-sdk</artifactId>
             <version>1.7.0</version>
        </dependency>
    </dependencies>
   ```

1. Copie o conteúdo do **Windows-x64** para o local do projeto Java, por exemplo, **C:\SDSDK\JRE-Sample-Release**

1. Copie `kws.table`, `participants.properties` e `Microsoft.CognitiveServices.Speech.extension.pma.dll` na pasta do projeto **target\classes**

## <a name="configure-the-sample-application"></a>Configurar o aplicativo de exemplo

1. Adicione sua chave de assinatura de fala ao código-fonte. Se quiser experimentar o reconhecimento da intenção, adicione também seu [serviço de compreensão de idiomas](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) ID da chave de subscrição e a aplicação.

   Para fala e LUIS, suas informações entram em `FunctionsList.java`:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Se você estiver usando a transcrição de conversa, suas informações de chave de fala e região também serão necessárias no `Cts.java`:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. A palavra-chave default (palavra-chave) é "Computer". Você também pode experimentar uma das outras palavras-chave fornecidas, como "Machine" ou "Assistant". Os arquivos de recurso para essas palavras-chave alternativas estão no SDK de dispositivos de fala, na pasta de palavras-chave. Por exemplo, `C:\SDSDK\JRE-Sample-Release\keyword\Computer` contém os arquivos usados para a palavra-chave "Computer".

    > [!TIP]
    > Você também pode [criar uma palavra-chave personalizada](speech-devices-sdk-create-kws.md).

    Para usar uma nova palavra-chave, atualize a linha a seguir em `FunctionsList.java`e copie a palavra-chave para seu aplicativo. Por exemplo, para usar a palavra-chave ' Machine ' do pacote de palavras-chave `machine.zip`:

   * Copie o arquivo de `kws.table` do pacote zip para as **classes/destino**da pasta do projeto.
   * Atualize o `FunctionsList.java` com o nome da palavra-chave:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Executar o aplicativo de exemplo do eclipse

1. Na barra de menus do eclipse, **execute** > **Executar como** > **aplicativo Java**. Em seguida, selecione **functionlist** e **OK**.

   ![Captura de tela de selecionar aplicativo Java](media/speech-devices-sdk/eclipse-run-sample.png)

1. O aplicativo de exemplo do SDK de dispositivos de fala é iniciado e exibe as seguintes opções:

   ![Exemplo de aplicativo de exemplo de SDK de dispositivos de voz e opções](media/speech-devices-sdk/java-sample-app-windows.png)

1. Experimente a nova demonstração de **transcrição de conversa** . Comece a transcrever com a **sessão** > **Iniciar**. Por padrão, todos são convidados. No entanto, se você tiver as assinaturas de voz do participante, elas poderão ser colocadas em um arquivo `participants.properties` nas **classes/destino**da pasta do projeto. Para gerar a assinatura de voz, examine [conversações (SDK) de transcrever](how-to-use-conversation-transcription-service.md).

   ![Aplicativo de transcrição de conversa de demonstração](media/speech-devices-sdk/cts-sample-app-windows.png)

## <a name="create-and-run-a-standalone-application"></a>Criar e executar um aplicativo autônomo

1. No **Explorador de pacotes**, clique com o botão direito do mouse em seu projeto. Escolha **Exportar**.

1. A janela **Exportar** é exibida. Expanda **Java** e selecione **arquivo JAR executável** e, em seguida, selecione **Avançar**.

   ![Captura de tela da janela de exportação](media/speech-devices-sdk/eclipse-export-windows.png)

1. A janela de **exportação de arquivo JAR executável** é exibida. Escolha um **destino de exportação** para o aplicativo e, em seguida, selecione **concluir**.

   ![Captura de tela de exportação de arquivo JAR executável](media/speech-devices-sdk/eclipse-export-jar-windows.png)

1. Coloque `kws.table`, `participants.properties`, `unimic_runtime.dll`, `pma.dll` e `Microsoft.CognitiveServices.Speech.extension.pma.dll` na pasta de destino escolhida acima, pois esses arquivos são necessários para o aplicativo.

1. Para executar o aplicativo autônomo

   ```powershell
   java -jar SpeechDemo.jar
   ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Reveja as notas de versão](devices-sdk-release-notes.md)
