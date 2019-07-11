---
title: 'Início rápido: Executar os SDK de dispositivos de fala no Windows - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Pré-requisitos e instruções para começar a trabalhar com um SDK de dispositivos de fala do Windows.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: erhopf
ms.openlocfilehash: ac0ed218aa27a36b3b8cd8ed8123e2baef6948c6
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723385"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-windows"></a>Início rápido: Execute a aplicação de exemplo do SDK de dispositivos de voz no Windows

Neste início rápido, irá aprender como usar a fala dispositivos SDK para Windows para criar um produto com funcionalidade de voz ou usá-la como uma [transcrição da conversação](conversation-transcription-service.md) dispositivo. Atualmente, apenas os [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) é suportada.

A aplicação baseia-se com o pacote do SDK de voz e o IDE de Java de Eclipse (v4) no Windows de 64 bits. É executada num ambiente de tempo de execução Java 8 de 64 bits (JRE).

Este guia requer uma [serviços cognitivos do Azure](get-started.md) conta com um recurso de serviços de voz. Se não tiver uma conta, pode utilizar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de subscrição.

O código-fonte para o [aplicação de exemplo](https://aka.ms/sdsdk-download-JRE) está incluído com o SDK de dispositivos de voz. Também vale [está disponível no GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Sistema operativo: Windows de 64 bits
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) apenas.
* [Microsoft Visual C++ Redistributable](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)
* Uma chave de subscrição do Azure para o serviço de voz. [Obter uma gratuitamente](get-started.md).
* Baixe a versão mais recente dos [SDK de dispositivos de voz](https://aka.ms/sdsdk-download-JRE) para Java e extrair o. zip para o seu diretório de trabalho.
   > [!NOTE]
   > O ficheiro de exemplo-JRE-Release.zip inclui a aplicação de exemplo JRE e este guia de introdução pressupõe que a aplicação é extraída para C:\SDSDK\JRE-Sample-Release

Transcrição da conversação está atualmente disponível apenas para "en-US" e "zh-CN", nas regiões "centralus" e "Sudeste Asiático". Tem de ter uma chave de voz em uma dessas regiões a utilizar a transcrição da conversação.

Se planeja usar as intenções, terá uma [serviço de compreensão de idiomas (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) subscrição. Para saber mais sobre o LUIS e reconhecimento da intenção, veja [reconhecer intenções de voz com os LUIS, C# ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). R [exemplo de modelo do LUIS](https://aka.ms/sdsdk-luis) está disponível para esta aplicação.

## <a name="create-and-configure-the-project"></a>Criar e configurar o projeto

1. Inicie o Eclipse.

1. Na **iniciador de IDE do Eclipse**, na **área de trabalho** , insira o nome de um novo diretório de área de trabalho. Em seguida, selecione **Iniciar**.

   ![Captura de ecrã do Iniciador do Eclipse](media/speech-devices-sdk/eclipse-launcher.png)

1. Dentro de momentos, é apresentada a janela principal do IDE do Eclipse. Feche o ecrã de Boas-Vindas, se existir.

1. Na barra de menus Eclipse, crie um novo projeto escolhendo **arquivo** > **New** > **projeto Java**. Se não está disponível escolher **Project** e, em seguida **projeto Java**.

1. O **novo projeto de Java** wizard é iniciado. **Procurar** para a localização do projeto de exemplo. Selecione **Concluir**.

   ![Captura de ecrã do assistente Novo Projeto Java](media/speech-devices-sdk/eclipse-new-java-project.png)

1. Na **Explorador de pacotes**, clique no projeto. Escolha **Configurar** > **Converter em Projeto Maven** no menu de contexto. Selecione **Concluir**.

   ![Captura de ecrã do Explorador de pacotes](media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Cópia `kws.table`, `participants.properties` e `Microsoft.CognitiveServices.Speech.extension.pma.dll` na pasta do projeto **target\classes**

## <a name="configure-the-sample-application"></a>Configurar a aplicação de exemplo

1. Adicione a chave de subscrição de voz ao código-fonte. Se quiser experimentar o reconhecimento da intenção, adicione também seu [serviço de compreensão de idiomas](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) ID da chave de subscrição e a aplicação.

   Para conversão de voz e o LUIS, suas informações entram em `FunctionsList.java`:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Se estiver a utilizar transcrição da conversação, suas informações de chave e a região de voz também são necessários em `Cts.java`:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. A palavra de reativação de predefinição (palavra-chave) é o "Computador". Também pode tentar uma das outras fornecidas reativação palavras, como "Máquina" ou "Assistente". Os ficheiros de recursos para essas palavras de reativação alternativo são no SDK de dispositivos de voz, na pasta de palavra-chave. Por exemplo, `C:\SDSDK\JRE-Sample-Release\keyword\Computer` contém os arquivos usados para a palavra de reativação "Computador".

   > [!TIP]
   > Também pode [criar uma palavra de reativação personalizado](speech-devices-sdk-create-kws.md).

    Para utilizar uma nova palavra de reativação, Atualize as duas linhas seguintes no `FunctionsList.java`e copie o pacote de reativação do word para a sua aplicação. Por exemplo, para utilizar a palavra de reativação "Machine" do pacote de reativação word `kws-machine.zip`:

   * Copie o pacote de reativação do word na pasta do projeto **destino/classes**.

   * Atualização do `FunctionsList.java` com a palavra-chave e o nome do pacote:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Executar a aplicação de exemplo a partir do Eclipse

1. Na barra de menus Eclipse, **execute** > **Run As** > **aplicação Java**. Em seguida, selecione **FunctionsList** e **OK**.

   ![Captura de ecrã da aplicação Java selecione](media/speech-devices-sdk/eclipse-run-sample.png)

1. O aplicativo de exemplo do SDK de dispositivos de fala é iniciado e exibe as seguintes opções:

   ![Exemplo de aplicativo de exemplo de SDK de dispositivos de voz e opções](media/speech-devices-sdk/java-sample-app-windows.png)

1. Experimente a nova **transcrição da conversação** demonstração. Iniciar Fotografar com **sessão** > **iniciar**. Por predefinição, todas as pessoas são um convidado. No entanto, se tiver de assinaturas de voz de um participante podem ser colocados num arquivo `participants.properties` na pasta de projeto **destino/classes**. Para gerar a assinatura de voz, observe [Transcreva conversas (SDK)](how-to-use-conversation-transcription-service.md).

   ![Aplicação de transcrição da conversação de demonstração](media/speech-devices-sdk/cts-sample-app-windows.png)

## <a name="create-and-run-a-standalone-application"></a>Criar e executar uma aplicação autónoma

1. Na **Explorador de pacotes**, clique no projeto. Escolher **exportar**. 

1. O **exportar** é apresentada a janela. Expanda **Java** e selecione **ficheiro JAR executáveis** e, em seguida, selecione **seguinte**.

   ![Captura de ecrã da janela de exportação](media/speech-devices-sdk/eclipse-export-windows.png) 

1. O **executáveis JAR ficheiro exportar** é apresentada a janela. Escolher uma **destino de exportação** da aplicação e, em seguida, selecione **concluir**.
 
   ![Captura de ecrã da exportação de ficheiro JAR executáveis](media/speech-devices-sdk/eclipse-export-jar-windows.png)

1. Coloque `kws.table`, `participants.properties`, `unimic_runtime.dll`, `pma.dll` e `Microsoft.CognitiveServices.Speech.extension.pma.dll` na pasta de destino escolhida acima como esses arquivos são exigidos pelo aplicativo.

1. Para executar a aplicação autónoma

     ```powershell
     java -jar SpeechDemo.jar
     ```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Reveja as notas de versão](devices-sdk-release-notes.md)
