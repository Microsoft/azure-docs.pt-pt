---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: d45986dcd8b846015abfef9cb3719d0107c6b8d6
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400125"
---
Neste arranque rápido, aprenderá a usar o SDK de Dispositivos de Fala para o Linux para construir um produto ativado pela fala ou usá-lo como um dispositivo de Transcrição de [Conversas.](../conversation-transcription-service.md) Atualmente, apenas o [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) é suportado.

A aplicação é construída com o pacote Speech SDK, e o Eclipse Java IDE (v4) em 64 bits Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8). É executada num ambiente de tempo de execução Java 8 de 64 bits (JRE).

Este guia requer uma conta [de Serviços Cognitivos Azure](../get-started.md) com um recurso de serviço da Fala. Se não tiver uma conta, pode utilizar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de subscrição.

O código fonte para a [aplicação](https://aka.ms/sdsdk-download-JRE) da amostra está incluído no SDK dos Dispositivos de Fala. Também está [disponível no GitHub.](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Sistema operativo: Linux de 64 bits (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8)
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Apenas Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8.](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Uma chave de subscrição Azure para o serviço De Fala. [Arranja um de graça.](../get-started.md)
* Descarregue a versão mais recente do [SDK](https://aka.ms/sdsdk-download-JRE) dos Dispositivos de Fala para Java e extraa o .zip para o seu diretório de trabalho.
   > [!NOTE]
   > Este quickstart pressupõe que a aplicação é extraída para /home/wcaltest/JRE-Sample-Release

Certifique-se de que estas dependências estão instaladas antes de iniciar o Eclipse.

* Em Ubuntu:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* Em Debian 9:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

No RHEL/CentOS 8:

  ```sh
  sudo yum update
  sudo yum install alsa-lib openssl
  ```

> [!NOTE]
> No RHEL/CentOS 8, siga as instruções sobre como configurar o [OpenSSL para Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

A tualmente, a Transcrição de Conversas só está disponível para "en-US" e "zh-CN", nas regiões "central" e "eastasia". Deve ter uma chave de discurso numa dessas regiões para usar a Transcrição de Conversações.

Se planeia utilizar as intenções, necessitará de uma subscrição do Serviço de [Compreensão de Línguas (LUIS).](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) Para saber mais sobre LUIS e reconhecimento de intenções, consulte [Reconhecer as intenções de fala com LUIS, C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). Está disponível uma amostra do [modelo LUIS](https://aka.ms/sdsdk-luis) para esta aplicação.

## <a name="create-and-configure-the-project"></a>Criar e configurar o projeto

1. Inicie o Eclipse.

1. No **Launcher Eclipse IDE**, na área **workspace,** introduza o nome de um novo diretório de espaço de trabalho. Em seguida, selecione **Iniciar**.

   ![Captura de ecrã do Iniciador do Eclipse](../media/speech-devices-sdk/eclipse-launcher-linux.png)

1. Dentro de momentos, é apresentada a janela principal do IDE do Eclipse. Feche o ecrã de Boas-Vindas, se existir.

1. A partir da barra de menueclipse, crie um novo projeto escolhendo **File** > **New** > **Java Project**. Se não estiver disponível escolha **Projeto** e, em seguida, **Projeto Java**.

1. O feiticeiro do **Projeto New Java** começa. **Procure** a localização do projeto da amostra. Selecione **Concluir**.

   ![Captura de ecrã do assistente Novo Projeto Java](../media/speech-devices-sdk/eclipse-new-java-project-linux.png)

1. No **explorador**de pacotes, clique no seu projeto. Escolha **Configure** > **Converter para Maven Project** a partir do menu de contexto. Selecione **Concluir**.

   ![Captura de ecrã do Explorador de pacotes](../media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Abra o ficheiro pom.xml e edite-o.

    No final do ficheiro, antes `</project>`da `repositories` etiqueta `dependencies` de fecho, crie e `version` elementos, como mostrado aqui, e certifique-se de que a versão atual corresponde:
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
             <version>1.11.0</version>
        </dependency>
    </dependencies>
   ```

1. No **explorador**de pacotes, clique no seu projeto. Escolha **propriedades,** em **seguida, executar/depurar definições** > **Novas...** > **Aplicação Java**. 

1. Aparece a janela **de Configuração de Edição.** No campo **Nome** introduza **Main**, e use **Search** for the **Main Class** para encontrar e selecionar **com.microsoft.cognitiveservices.speech.samples.FunctionsList**.

   ![Screenshot da configuração de lançamento de edição](../media/speech-devices-sdk/eclipse-edit-launch-configuration-linux.png)

1. Copie os binários de áudio para a sua arquitetura alvo, do **braço de Linux** ou **do Linux-x64,** para a localização do Projeto Java, por **exemplo/home/wcaltest/JRE-Sample-Release**

1. Também a partir da janela **de configuração editar** selecione a página **Ambiente** e **Nova**. A janela **New Environment Variable** aparece. No campo **Nome** introduza **LD_LIBRARY_PATH** e no campo de **valor** introduza a pasta que contém os ficheiros *.so, por **exemplo/home/wcaltest/JRE-Sample-Release**

1. Copiar `kws.table` `participants.properties` e entrar no **alvo/classes** da pasta do projeto


## <a name="configure-the-sample-application"></a>Configurar o exemplo de aplicação

1. Adicione a chave de subscrição do seu discurso ao código fonte. Se quiser experimentar o reconhecimento de intenções, adicione também a chave de subscrição do [serviço De compreensão](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) de idiomas e o ID da aplicação.

   Para a fala e para `FunctionsList.java`o LUIS, a sua informação vai para:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Se estiver a utilizar a transcrição da conversação, `Cts.java`a sua chave de fala e informações da região também são necessárias em:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. A palavra-chave padrão (palavra-chave) é "Computador". Também pode experimentar uma das outras palavras-chave fornecidas, como "Máquina" ou "Assistente". Os ficheiros de recursos para estas palavras-chave alternativas estão no SDK dos Dispositivos de Fala, na pasta das palavras-chave. Por exemplo, `/home/wcaltest/JRE-Sample-Release/keyword/Computer` contém os ficheiros utilizados para a palavra-chave "Computador".

   > [!TIP]
   > Também pode [criar uma palavra-chave personalizada.](../speech-devices-sdk-create-kws.md)

    Para utilizar uma nova palavra-chave, `FunctionsList.java`atualize a seguinte linha e copie a palavra-chave para a sua aplicação. Por exemplo, utilizar a palavra-chave 'Máquina' `machine.zip`do pacote de palavras-chave:

   * Copie `kws.table` o ficheiro do pacote zip para o **alvo/classes**da pasta do projeto .

   * Atualize `FunctionsList.java` o com o nome da palavra-chave:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Executar a aplicação da amostra a partir do Eclipse

1. Da barra de menu Eclipse, **Run** > **Run** 

1. A aplicação exemplo sDK dispositivos de fala começa e apresenta as seguintes opções:

   ![Aplicação e opções de exemplo sdk dispositivos de fala de amostra](../media/speech-devices-sdk/java-sample-app-linux.png)

1. Experimente a nova demonstração de Transcrição de **Conversas.** Comece a transcrever com o**Início**de **Sessão** > . Por defeito, todos são convidados. No entanto, se tiver as assinaturas `participants.properties` de voz dos participantes, podem ser colocadas no **alvo/classes**da pasta do projeto . Para gerar a assinatura de voz, veja as [conversas do Transcribe (SDK)](../how-to-use-conversation-transcription-service.md).

   ![Aplicação de transcrição de conversação de demonstração](../media/speech-devices-sdk/cts-sample-app-linux.png)

## <a name="create-and-run-standalone-the-application"></a>Criar e executar autónomamente a aplicação

1. No **explorador**de pacotes, clique no seu projeto. Escolha **exportar**. 
1. A janela **de exportação** aparece. Expanda **Java** e selecione **ficheiro JAR Runnable** e, em seguida, selecione **Next**.

   ![Screenshot da janela exportação](../media/speech-devices-sdk/eclipse-export-linux.png) 

1. Aparece a janela **runnável de exportação de ficheiros JAR.** Escolha um **destino de exportação** para a aplicação e, em seguida, selecione **Terminar**.
 
   ![Screenshot da exportação de ficheiros JAR Runnable](../media/speech-devices-sdk/eclipse-export-jar-linux.png)

1. Por `kws.table` favor, coloque e `participants.properties` na pasta de destino escolhida acima, uma vez que estes ficheiros são necessários pela aplicação.

1. Desloque a LD_LIBRARY_LIB para a pasta que contém os ficheiros *.so

     ```bash
     export LD_LIBRARY_PATH=/home/wcaltest/JRE-Sample-Release
     ```

1. Para executar a aplicação autónoma

     ```bash
     java -jar SpeechDemo.jar
     ```
