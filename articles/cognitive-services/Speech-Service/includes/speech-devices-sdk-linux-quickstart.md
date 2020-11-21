---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: 3c093539301f3f4e3309094f0b53a1f0e8393150
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95098431"
---
Neste arranque rápido, aprenderá a usar o SDK de Dispositivos de Fala para o Linux para construir um produto ativado pela fala ou usá-lo como dispositivo [de transcrição de conversação.](../conversation-transcription.md) Atualmente apenas o [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) é suportado.

A aplicação é construída com o pacote Speech SDK, e o Eclipse Java IDE (v4) em Linux de 64 bits (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 7/8, CentOS 7/8). É executada num ambiente de tempo de execução Java 8 de 64 bits (JRE).

Este guia requer uma conta [Azure Cognitive Services](../overview.md#try-the-speech-service-for-free) com um recurso de serviço de fala. 

O código-fonte da [aplicação](https://aka.ms/sdsdk-download-JRE) da amostra está incluído no SDK dos Dispositivos de Fala. Também está [disponível no GitHub.](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Sistema Operativo: Linux de 64 bits (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 7/8, CentOS 7/8)
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Só java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8.](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Uma chave de subscrição Azure para o serviço Speech. [Arranja um de graça.](../overview.md#try-the-speech-service-for-free)
* Descarregue a versão mais recente do [Speech Devices SDK](https://aka.ms/sdsdk-download-JRE) para Java e extraia o .zip para o seu diretório de trabalho.
   > [!NOTE]
   > Este quickstart assume que a app é extraída para /home/wcaltest/JRE-Sample-Release

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

* No RHEL/CentOS:
  
  ```sh
  sudo yum update
  sudo yum install alsa-lib openssl
  ```

  > [!NOTE]
  > - No RHEL/CentOS 7, siga as instruções sobre [como configurar RHEL/CentOS 7 para A SDK de fala](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - No RHEL/CentOS 8, siga as instruções sobre como configurar o [OpenSSL para o Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

A transcrição da conversa está atualmente disponível apenas para as regiões "en-US" e "zh-CN", nas regiões "central" e "eastasia". Deve ter uma chave de discurso numa dessas regiões para usar a Transcrição de Conversação.

Se pretender utilizar as intenções, precisará de uma subscrição do [Serviço de Compreensão de Línguas (LUIS).](../../luis/luis-how-to-azure-subscription.md) Para saber mais sobre o LUIS e o reconhecimento de intenções, consulte [Reconhecer as intenções da fala com LUIS, C.](../how-to-recognize-intents-from-speech-csharp.md). Para esta aplicação está disponível uma [amostra de modelo LUIS.](https://aka.ms/sdsdk-luis)

## <a name="create-and-configure-the-project"></a>Criar e configurar o projeto

1. Inicie o Eclipse.

1. No **Eclipse IDE Launcher,** no campo **Workspace,** insira o nome de um novo diretório de espaço de trabalho. Em seguida, selecione **Iniciar**.

   ![Screenshot que mostra o Launcher Eclipse.](../media/speech-devices-sdk/eclipse-launcher-linux.png)

1. Dentro de momentos, é apresentada a janela principal do IDE do Eclipse. Feche o ecrã de Boas-Vindas, se existir.

1. A partir da barra de menus Eclipse, crie um novo projeto escolhendo **File**  >  **New**  >  **Java Project.** Se não estiver disponível, escolha **o Projeto** e, em seguida, o **Projeto Java.**

1. O novo assistente **do Projeto Java** começa. **Procure** a localização do projeto da amostra. Selecione **Concluir**.

   ![Captura de ecrã do assistente Novo Projeto Java](../media/speech-devices-sdk/eclipse-new-java-project-linux.png)

1. No **explorador do Pacote,** clique com o botão direito do seu projeto. Escolha **Configurar**  >  **Converter para Projeto Maven** a partir do menu de contexto. Selecione **Concluir**.

   ![Captura de ecrã do Explorador de pacotes](../media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Abra o ficheiro pom.xml e edite-o.

    No final do ficheiro, antes da etiqueta de `</project>` fecho, crie `repositories` e `dependencies` elementos, como mostrado aqui, e certifique-se de que corresponde à `version` sua versão atual:
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
             <version>1.14.0</version>
        </dependency>
    </dependencies>
   ```

1. No **explorador do Pacote,** clique com o botão direito do seu projeto. Escolha **propriedades** e, em seguida, **executar/depurar configurações**  >  **novas...** > **Aplicação Java**. 

1. Aparece a janela **de configuração de edição.** No campo **Nome** insira **principal**, e use **Search** for the **Main Class** para encontrar e selecionar **com.microsoft.cognitiveservices.speech.samples.FunctionsList**.

   ![Screenshot da configuração de lançamento de edição](../media/speech-devices-sdk/eclipse-edit-launch-configuration-linux.png)

1. Copie os binários de áudio para a sua arquitetura-alvo, desde **o braço Linux** ou **Linux-x64,** até à localização do Projeto Java, por exemplo **/home/wcaltest/JRE-Sample-Release**

1. Também a partir da janela **de Configuração de Edição** selecione a página **Ambiente** e **Nova**. Aparece a nova janela **da Variável ambiente.** No campo **Nome** introduza **LD_LIBRARY_PATH** e no campo de **valor** introduza a pasta que contém os ficheiros *.soo, por exemplo **/home/wcaltest/JRE-Sample-Release**

1. Copiar `kws.table` e entrar no `participants.properties` **alvo/classes** da pasta do projeto


## <a name="configure-the-sample-application"></a>Configurar o exemplo de aplicação

1. Adicione a chave de subscrição de discurso ao código fonte. Se quiser tentar o reconhecimento de intenções, adicione também a chave de subscrição do [serviço de Compreensão linguística](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) e o ID da aplicação.

   Para a fala e o LUIS, a sua informação vai `FunctionsList.java` para:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Se estiver a utilizar a transcrição da conversa, a sua chave de discurso e informação da região também são necessárias `Cts.java` em:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. A palavra-chave predefinida (palavra-chave) é "Computador". Também pode experimentar uma das outras palavras-chave fornecidas, como "Máquina" ou "Assistente". Os ficheiros de recursos para estas palavras-chave alternativas encontram-se na SDK dos Dispositivos de Fala, na pasta de palavras-chave. Por exemplo, `/home/wcaltest/JRE-Sample-Release/keyword/Computer` contém os ficheiros utilizados para a palavra-chave "Computador".

   > [!TIP]
   > Também pode [criar uma palavra-chave personalizada.](../custom-keyword-basics.md)

    Para utilizar uma nova palavra-chave, atualize a seguinte linha em `FunctionsList.java` , e copie a palavra-chave para a sua aplicação. Por exemplo, para utilizar a palavra-chave 'Máquina' a partir do pacote de palavras-chave `machine.zip` :

   * Copie o `kws.table` ficheiro do pacote zip para o **alvo/classes** da pasta do projeto .

   * Atualize o `FunctionsList.java` nome da palavra-chave:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Executar a aplicação da amostra a partir de Eclipse

1. A partir da barra de menu Eclipse, **Run**  >  **Run** 

1. A aplicação exemplo SDK dos Dispositivos de Fala começa e apresenta as seguintes opções:

   ![Screenshot que mostra uma aplicação e opções de exemplo SDK de dispositivos de fala.](../media/speech-devices-sdk/java-sample-app-linux.png)

1. Experimente a nova demonstração de **Transcrição** de Conversação. Comece a transcrever com **início de sessão**  >  **Start**. Por defeito, todos são convidados. No entanto, se tiver as assinaturas de voz dos participantes, podem ser colocadas `participants.properties` no **alvo/classes** da pasta do projeto . Para gerar a assinatura de voz, veja [as conversas transcrever (SDK)](../how-to-use-conversation-transcription.md).

   ![Screenshot que mostra uma aplicação de transcrição de conversação de demonstração.](../media/speech-devices-sdk/cts-sample-app-linux.png)

## <a name="create-and-run-standalone-the-application"></a>Criar e executar autónomo a aplicação

1. No **explorador do Pacote,** clique com o botão direito do seu projeto. Escolha **exportação.** 
1. Aparece a janela **de exportação.** Expanda **o Java** e selecione **o ficheiro RUNNable JAR** e, em seguida, selecione **Next**.

   ![Screenshot que mostra a janela exportação.](../media/speech-devices-sdk/eclipse-export-linux.png) 

1. Aparece a janela **runnable JAR File Export.** Escolha um **destino de exportação** para a aplicação e, em seguida, selecione **Acabamento**.
 
   ![Screenshot que mostra a janela runnable JAR File Export.](../media/speech-devices-sdk/eclipse-export-jar-linux.png)

1. Por favor, coloque `kws.table` e na pasta de destino escolhida `participants.properties` acima, uma vez que estes ficheiros são necessários pela aplicação.

1. Desa esta LD_LIBRARY_LIB à pasta que contém os ficheiros *.so

     ```bash
     export LD_LIBRARY_PATH=/home/wcaltest/JRE-Sample-Release
     ```

1. Para executar a aplicação autónoma

     ```bash
     java -jar SpeechDemo.jar
     ```