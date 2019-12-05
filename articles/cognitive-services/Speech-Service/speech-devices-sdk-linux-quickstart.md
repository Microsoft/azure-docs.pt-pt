---
title: 'Início rápido: executar o SDK de dispositivos de fala no serviço de fala do Linux'
titleSuffix: Azure Cognitive Services
description: Pré-requisitos e instruções para começar a usar um SDK de dispositivos de fala do Linux.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/13/2019
ms.author: erhopf
ms.openlocfilehash: c9175f1f592c72e047b02a06a174d3013b0c7ef6
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815319"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-linux"></a>Início rápido: executar o aplicativo de exemplo do SDK de dispositivos de fala no Linux

Neste guia de início rápido, você aprenderá a usar o SDK de dispositivos de fala para Linux para criar um produto habilitado para fala ou usá-lo como um dispositivo de [transcrição de conversa](conversation-transcription-service.md) . Atualmente, há suporte apenas para o [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) .

O aplicativo é criado com o pacote do SDK de fala e o eclipse Java IDE (v4) no Linux de 64 bits (Ubuntu 16, 4, Ubuntu 18, 4, Debian 9). É executada num ambiente de tempo de execução Java 8 de 64 bits (JRE).

Este guia requer uma conta de [Serviços cognitivas do Azure](get-started.md) com um recurso de serviço de fala. Se não tiver uma conta, pode utilizar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de subscrição.

O código-fonte do [aplicativo de exemplo](https://aka.ms/sdsdk-download-JRE) está incluído no SDK dos dispositivos de fala. Também vale [está disponível no GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Sistema operacional: Linux de 64 bits (Ubuntu 16, 4, Ubuntu 18, 4, Debian 9)
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Java IDE Eclipse](https://www.eclipse.org/downloads/)
* Somente [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) .
* Uma chave de assinatura do Azure para o serviço de fala. [Obtenha um gratuitamente](get-started.md).
* Baixe a versão mais recente do [SDK de dispositivos de fala](https://aka.ms/sdsdk-download-JRE) para Java e extraia o. zip para seu diretório de trabalho.
   > [!NOTE]
   > O arquivo JRE-Sample-Release. zip inclui o aplicativo de exemplo do JRE e este guia de início rápido pressupõe que o aplicativo é extraído para/home/wcaltest/JRE-Sample-Release

Verifique se essas dependências estão instaladas antes de iniciar o eclipse.

* No Ubuntu:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* No Debian 9:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

Atualmente, a transcrição de conversa está disponível apenas para "en-US" e "zh-CN", nas regiões "centralus" e "eastasia". Você deve ter uma chave de fala em uma dessas regiões para usar a transcrição de conversa.

Se você planeja usar as intenções, precisará de uma assinatura do [Luis (serviço de reconhecimento vocal)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) . Para saber mais sobre o LUIS e o reconhecimento de intenção, consulte [reconhecer tentativas de fala com C#o Luis, ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). Um [modelo Luis de exemplo](https://aka.ms/sdsdk-luis) está disponível para este aplicativo.

## <a name="create-and-configure-the-project"></a>Criar e configurar o projeto

1. Inicie o Eclipse.

1. No **iniciador do IDE do eclipse**, no campo **espaço de trabalho** , digite o nome de um novo diretório de espaço de trabalho. Em seguida, selecione **Iniciar**.

   ![Captura de ecrã do Iniciador do Eclipse](media/speech-devices-sdk/eclipse-launcher-linux.png)

1. Dentro de momentos, é apresentada a janela principal do IDE do Eclipse. Feche o ecrã de Boas-Vindas, se existir.

1. Na barra de menus do eclipse, crie um novo projeto escolhendo **arquivo** > **novo** > **projeto Java**. Se não estiver disponível, escolha **projeto** e, em seguida, **projeto Java**.

1. O assistente de **novo projeto Java** é iniciado. **Procure** o local do projeto de exemplo. Selecione **Concluir**.

   ![Captura de ecrã do assistente Novo Projeto Java](media/speech-devices-sdk/eclipse-new-java-project-linux.png)

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

1. No **Explorador de pacotes**, clique com o botão direito do mouse em seu projeto. Escolha **Propriedades**e, em seguida, **executar/depurar configurações** > **novo...** > **aplicativo Java**. 

1. A janela **Editar configuração** é exibida. No campo **nome** , insira **principal**e use **procurar** a **classe principal** para localizar e selecionar **com. Microsoft. cognitivaservices. Speech. Samples. functionlist**.

   ![Captura de tela de Editar configuração de inicialização](media/speech-devices-sdk/eclipse-edit-launch-configuration-linux.png)

1. Copie os binários de áudio para sua arquitetura de destino, do **Linux-ARM** ou **Linux-x64**, para o local do projeto Java, por exemplo, **/Home/wcaltest/JRE-Sample-Release**

1. Também na janela **Editar configuração** , selecione a página **ambiente** e **novo**. A janela **nova variável de ambiente** é exibida. No campo **nome** , insira **LD_LIBRARY_PATH** e, no campo **valor** , insira a pasta que contém os arquivos *. so, por exemplo, **/Home/wcaltest/JRE-Sample-Release**

1. Copiar `kws.table` e `participants.properties` no **destino/classes** da pasta do projeto


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

1. A palavra-chave default (palavra-chave) é "Computer". Você também pode experimentar uma das outras palavras-chave fornecidas, como "Machine" ou "Assistant". Os arquivos de recurso para essas palavras-chave alternativas estão no SDK de dispositivos de fala, na pasta de palavras-chave. Por exemplo, `/home/wcaltest/JRE-Sample-Release/keyword/Computer` contém os arquivos usados para a palavra-chave "Computer".

   > [!TIP]
   > Você também pode [criar uma palavra-chave personalizada](speech-devices-sdk-create-kws.md).

    Para usar uma nova palavra-chave, atualize a linha a seguir em `FunctionsList.java`e copie a palavra-chave para seu aplicativo. Por exemplo, para usar a palavra-chave ' Machine ' do pacote de palavras-chave `machine.zip`:

   * Copie o arquivo de `kws.table` do pacote zip para as **classes/destino**da pasta do projeto.

   * Atualize o `FunctionsList.java` com o nome da palavra-chave:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Executar o aplicativo de exemplo do eclipse

1. Na barra de menus do eclipse, **execute** > **executar** 

1. O aplicativo de exemplo do SDK de dispositivos de fala é iniciado e exibe as seguintes opções:

   ![Exemplo de aplicativo de exemplo de SDK de dispositivos de voz e opções](media/speech-devices-sdk/java-sample-app-linux.png)

1. Experimente a nova demonstração de **transcrição de conversa** . Comece a transcrever com a **sessão** > **Iniciar**. Por padrão, todos são convidados. No entanto, se você tiver as assinaturas de voz do participante, elas poderão ser colocadas em `participants.properties` nas **classes/destino**da pasta do projeto. Para gerar a assinatura de voz, examine [conversações (SDK) de transcrever](how-to-use-conversation-transcription-service.md).

   ![Aplicativo de transcrição de conversa de demonstração](media/speech-devices-sdk/cts-sample-app-linux.png)

## <a name="create-and-run-standalone-the-application"></a>Criar e executar o aplicativo autônomo

1. No **Explorador de pacotes**, clique com o botão direito do mouse em seu projeto. Escolha **Exportar**. 
1. A janela **Exportar** é exibida. Expanda **Java** e selecione **arquivo JAR executável** e, em seguida, selecione **Avançar**.

   ![Captura de tela da janela de exportação](media/speech-devices-sdk/eclipse-export-linux.png) 

1. A janela de **exportação de arquivo JAR executável** é exibida. Escolha um **destino de exportação** para o aplicativo e, em seguida, selecione **concluir**.
 
   ![Captura de tela de exportação de arquivo JAR executável](media/speech-devices-sdk/eclipse-export-jar-linux.png)

1. Coloque `kws.table` e `participants.properties` na pasta de destino escolhida acima, pois esses arquivos são necessários para o aplicativo.

1. Defina o LD_LIBRARY_LIB para a pasta que contém os arquivos *. so

     ```bash
     export LD_LIBRARY_PATH=/home/wcaltest/JRE-Sample-Release
     ```

1. Para executar o aplicativo autônomo

     ```bash
     java -jar SpeechDemo.jar
     ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Reveja as notas de versão](devices-sdk-release-notes.md)
