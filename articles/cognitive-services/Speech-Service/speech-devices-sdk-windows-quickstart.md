---
title: 'Quickstart: Executar os dispositivos de fala SDK no Windows - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Pré-requisitos e instruções para começar com um SDK dispositivos de discurso do Windows.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 02/12/2020
ms.author: erhopf
ms.openlocfilehash: 78d9dc8c2302de6a9bdbfef62d47d10d252cf0c5
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77189032"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-windows"></a>Quickstart: Executar a aplicação de amostras SDK dispositivos de fala no Windows

Neste arranque rápido, aprenderá a usar o SDK dos Dispositivos de Fala para o Windows para construir um produto ativado pela fala ou usá-lo como um dispositivo de Transcrição de [Conversas.](conversation-transcription-service.md) Para a Transcrição de Conversas apenas o [DK Azure Kinect](https://azure.microsoft.com/services/kinect-dk/) é suportado. Para outros, utilize matrizes de microfoneline que fornecem uma geometria de matriz de microfone são suportadas.

A aplicação é construída com o pacote Speech SDK, e o Eclipse Java IDE (v4) em Windows de 64 bits. É executada num ambiente de tempo de execução Java 8 de 64 bits (JRE).

Este guia requer uma conta [de Serviços Cognitivos Azure](get-started.md) com um recurso de serviço da Fala. Se não tiver uma conta, pode utilizar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de subscrição.

O código fonte para a [aplicação](https://aka.ms/sdsdk-download-JRE) da amostra está incluído no SDK dos Dispositivos de Fala. Também está [disponível no GitHub.](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Sistema operativo: Windows de 64 bits
* Uma matriz de microfone como [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Apenas Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8.](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Microsoft Visual C++ Redistributable](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)
* Uma chave de subscrição Azure para o serviço De Fala. [Arranja um de graça.](get-started.md)
* Descarregue a versão mais recente do [SDK](https://aka.ms/sdsdk-download-JRE) dos Dispositivos de Fala para Java e extraa o .zip para o seu diretório de trabalho.
   > [!NOTE]
   > Este quickstart pressupõe que a aplicação é extraída para C:\SDSDK\JRE-Sample-Release

A tualmente, a Transcrição de Conversas só está disponível para "en-US" e "zh-CN", nas regiões "central" e "eastasia". Deve ter uma chave de discurso numa dessas regiões para usar a Transcrição de Conversações.

Se planeia utilizar as intenções, necessitará de uma subscrição do Serviço de [Compreensão de Línguas (LUIS).](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) Para saber mais sobre LUIS e reconhecimento de intenções, consulte [Reconhecer as intenções de fala com C#LUIS, ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). Está disponível uma amostra do [modelo LUIS](https://aka.ms/sdsdk-luis) para esta aplicação.

## <a name="create-and-configure-the-project"></a>Criar e configurar o projeto

1. Inicie o Eclipse.

1. No **Launcher Eclipse IDE**, na área **workspace,** introduza o nome de um novo diretório de espaço de trabalho. Em seguida, selecione **Iniciar**.

   ![Captura de ecrã do Iniciador do Eclipse](media/speech-devices-sdk/eclipse-launcher.png)

1. Dentro de momentos, é apresentada a janela principal do IDE do Eclipse. Feche o ecrã de Boas-Vindas, se existir.

1. A partir da barra de menueclipse, crie um novo projeto escolhendo **File** > **New** > **Java Project.** Se não estiver disponível escolha **Projeto** e, em seguida, **Projeto Java**.

1. O feiticeiro do **Projeto New Java** começa. **Procure** a localização do projeto da amostra. Selecione **Concluir**.

   ![Captura de ecrã do assistente Novo Projeto Java](media/speech-devices-sdk/eclipse-new-java-project.png)

1. No **explorador**de pacotes, clique no seu projeto. Escolha **Configurar** > **Converter em Projeto Maven** no menu de contexto. Selecione **Concluir**.

   ![Captura de ecrã do Explorador de pacotes](media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Abra o ficheiro pom.xml e edite-o.

    No final do ficheiro, antes da etiqueta de fecho `</project>`, crie elementos `repositories` e `dependencies`, como mostrado aqui, e certifique-se de que o `version` corresponde à sua versão atual:
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
             <version>1.9.0</version>
        </dependency>
    </dependencies>
   ```

1. Copie o conteúdo do **Windows-x64** para a localização do Projeto Java, por exemplo **C:\SDSDK\JRE-Sample-Release**

1. Copiar `kws.table`, `participants.properties` e `Microsoft.CognitiveServices.Speech.extension.pma.dll` no alvo da pasta do **projeto\classes**

## <a name="configure-the-sample-application"></a>Configure a aplicação da amostra

1. Adicione a chave de subscrição do seu discurso ao código fonte. Se quiser experimentar o reconhecimento de intenções, adicione também a chave de subscrição do [serviço De compreensão](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) de idiomas e o ID da aplicação.

   Para a fala e para o LUIS, a sua informação vai para `FunctionsList.java`:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Se estiver a usar a transcrição da conversação, a sua chave de fala e informações da região também são necessárias em `Cts.java`:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. A palavra-chave padrão (palavra-chave) é "Computador". Também pode experimentar uma das outras palavras-chave fornecidas, como "Máquina" ou "Assistente". Os ficheiros de recursos para estas palavras-chave alternativas estão no SDK dos Dispositivos de Fala, na pasta das palavras-chave. Por exemplo, `C:\SDSDK\JRE-Sample-Release\keyword\Computer` contém os ficheiros utilizados para a palavra-chave "Computador".

    > [!TIP]
    > Também pode [criar uma palavra-chave personalizada.](speech-devices-sdk-create-kws.md)

    Para utilizar uma nova palavra-chave, atualize a seguinte linha em `FunctionsList.java`e copie a palavra-chave para a sua aplicação. Por exemplo, utilizar a palavra-chave "Máquina" do pacote de palavras-chave `machine.zip`:

   * Copie o ficheiro `kws.table` do pacote zip para o **alvo/classes**da pasta do projeto .
   * Atualize o `FunctionsList.java` com o nome da palavra-chave:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Executar a aplicação da amostra a partir do Eclipse

1. A partir da barra de menu eclipse, **run** > **Run As** > Java **Application**. Em seguida, selecione **FunctionsList** e **OK**.

   ![Screenshot da aplicação Select Java](media/speech-devices-sdk/eclipse-run-sample.png)

1. O aplicativo de exemplo do SDK de dispositivos de fala é iniciado e exibe as seguintes opções:

   ![Exemplo de aplicativo de exemplo de SDK de dispositivos de voz e opções](media/speech-devices-sdk/java-sample-app-windows.png)

1. Experimente a nova demonstração de Transcrição de **Conversas.** Comece a transcrever com **a Sessão** > **Iniciar**. Por defeito, todos são convidados. No entanto, se tiver as assinaturas de voz dos participantes, podem ser colocadas num ficheiro `participants.properties` no **alvo/classes**da pasta do projeto . Para gerar a assinatura de voz, veja as [conversas do Transcribe (SDK)](how-to-use-conversation-transcription-service.md).

   ![Aplicação de transcrição de conversação de demonstração](media/speech-devices-sdk/cts-sample-app-windows.png)

## <a name="create-and-run-a-standalone-application"></a>Criar e executar uma aplicação autónoma

1. No **explorador**de pacotes, clique no seu projeto. Escolha **exportar**.

1. A janela **de exportação** aparece. Expanda **Java** e selecione **ficheiro JAR Runnable** e, em seguida, selecione **Next**.

   ![Screenshot da janela exportação](media/speech-devices-sdk/eclipse-export-windows.png)

1. Aparece a janela **runnável de exportação de ficheiros JAR.** Escolha um **destino de exportação** para a aplicação e, em seguida, selecione **Terminar**.

   ![Screenshot da exportação de ficheiros JAR Runnable](media/speech-devices-sdk/eclipse-export-jar-windows.png)

1. Por favor, coloque `kws.table`, `participants.properties`, `unimic_runtime.dll`, `pma.dll` e `Microsoft.CognitiveServices.Speech.extension.pma.dll` na pasta de destino escolhida acima, uma vez que estes ficheiros são necessários pela aplicação.

1. Para executar a aplicação autónoma

   ```powershell
   java -jar SpeechDemo.jar
   ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Reveja as notas de lançamento](devices-sdk-release-notes.md)
