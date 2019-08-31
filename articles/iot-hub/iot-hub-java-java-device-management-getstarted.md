---
title: Introdução ao gerenciamento de dispositivo do Hub IoT do Azure (Java) | Microsoft Docs
description: Como usar o gerenciamento de dispositivos do Hub IoT do Azure para iniciar uma reinicialização remota do dispositivo. Você usa o SDK do dispositivo IoT do Azure para Java para implementar um aplicativo de dispositivo simulado que inclui um método direto e o SDK do serviço IoT do Azure para Java para implementar um aplicativo de serviço que invoca o método direto.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 52d7eb035ed06c368214b8b5cb7ef583cd99b51b
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70161990"
---
# <a name="get-started-with-device-management-java"></a>Introdução ao gerenciamento de dispositivo (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Este tutorial mostrar-lhe como:

* Use o portal do Azure para criar um hub IoT e criar uma identidade de dispositivo no Hub IoT.

* Crie um aplicativo de dispositivo simulado que implementa um método direto para reinicializar o dispositivo. Os métodos diretos são invocados da nuvem.

* Crie um aplicativo que invoque o método direto de reinicialização no aplicativo de dispositivo simulado por meio do Hub IoT. Esse aplicativo monitora as propriedades relatadas do dispositivo para ver quando a operação de reinicialização é concluída.

No final deste tutorial, você tem dois aplicativos de console do Java:

**Simulated-Device**. Este aplicativo:

* Conecta-se ao Hub IoT com a identidade do dispositivo criada anteriormente.

* Recebe uma chamada de método direto de reinicialização.

* Simula uma reinicialização física.

* Relata a hora da última reinicialização por meio de uma propriedade relatada.

**trigger-reboot**. Este aplicativo:

* Chama um método direto no aplicativo de dispositivo simulado.

* Exibe a resposta para a chamada de método direta enviada pelo dispositivo simulado.

* Exibe as propriedades relatadas atualizadas.

> [!NOTE]
> Para obter informações sobre os SDKs que você pode usar para criar aplicativos para execução em dispositivos e o back-end da solução, consulte [SDKs do IOT do Azure](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Java se Development Kit 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Certifique-se de selecionar **Java 8** em **suporte a longo prazo** para obter downloads para o JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no Hub IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Disparar uma reinicialização remota no dispositivo usando um método direto

Nesta seção, você cria um aplicativo de console Java que:

1. Invoca o método direto de reinicialização no aplicativo do dispositivo simulado.

2. Exibe a resposta.

3. Sonda as propriedades relatadas enviadas do dispositivo para determinar quando a reinicialização é concluída.

Esse aplicativo de console se conecta ao Hub IoT para invocar o método direto e ler as propriedades relatadas.

1. Crie uma pasta vazia chamada **DM-Get-Started**.

2. Na pasta **DM-Get-Started** , crie um projeto Maven chamado **Trigger-reboot** usando o seguinte comando no prompt de comando:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. No prompt de comando, navegue até a pasta **Trigger-reboot** .

4. Usando um editor de texto, abra o arquivo **pom. xml** na pasta **Trigger-reboot** e adicione a dependência a seguir ao nó **Dependencies** . Essa dependência permite que você use o pacote IOT-Service-Client em seu aplicativo para se comunicar com o Hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.17.1</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Você pode verificar a versão mais recente do **IOT-Service-Client** usando a [pesquisa do Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

5. Adicione o seguinte nó de **compilação** após o nó de **dependências** . Essa configuração instrui o Maven a usar o Java 1,8 para compilar o aplicativo:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. Salve e feche o arquivo **pom. xml** .

7. Usando um editor de texto, abra o arquivo de origem **Trigger-reboot\src\main\java\com\mycompany\app\app.java.** .

8. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

9. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. Substitua `{youriothubconnectionstring}` pela cadeia de conexão do Hub IOT que você copiou anteriormente em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string):

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. Para implementar um thread que leia as propriedades relatadas do dispositivo 10 a cada dez segundos, adicione a seguinte classe aninhada à classe **app** :

    ```java
    private static class ShowReportedProperties implements Runnable {
      public void run() {
        try {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          while (true) {
            System.out.println("Get reported properties from device twin");
            deviceTwins.getTwin(twinDevice);
            System.out.println(twinDevice.reportedPropertiesToString());
            Thread.sleep(10000);
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

11. Modifique a assinatura do método **principal** para gerar a seguinte exceção:

    ```java
    public static void main(String[] args) throws IOException
    ```

12. Para invocar o método direto de reinicialização no dispositivo simulado, substitua o código no método **Main** pelo código a seguir:

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      System.out.println("Invoke reboot direct method");
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, null);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }
      System.out.println("Invoked reboot on device");
      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }
    ```

13. Para iniciar o thread para sondar as propriedades relatadas do dispositivo simulado, adicione o seguinte código ao método **principal** :

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. Para permitir que você interrompa o aplicativo, adicione o seguinte código ao método **principal** :

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. Salve e feche o arquivo **Trigger-reboot\src\main\java\com\mycompany\app\app.java.** .

16. Crie o aplicativo de back-end do **gatilho-** reinicialização e corrija os erros. No prompt de comando, navegue até a pasta **Trigger-reboot** e execute o seguinte comando:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada

Nesta seção, você cria um aplicativo de console do Java que simula um dispositivo. O aplicativo escuta a chamada de método direto de reinicialização do Hub IoT e responde imediatamente a essa chamada. O aplicativo então é suspenso por um tempo para simular o processo de reinicialização antes de usar uma propriedade relatada para notificar o aplicativo de back-end de reinicialização do **gatilho** que a reinicialização foi concluída.

1. Na pasta **DM-Get-Started** , crie um projeto Maven chamado **Simulated-Device** usando o seguinte comando no prompt de comando:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. No prompt de comando, navegue até a pasta **Simulated-Device** .

3. Usando um editor de texto, abra o arquivo **pom. xml** na pasta **Simulated-Device** e adicione a dependência a seguir ao nó **Dependencies** . Essa dependência permite que você use o pacote IOT-Service-Client em seu aplicativo para se comunicar com o Hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Você pode verificar a versão mais recente do **IOT-Device-Client** usando a [pesquisa do Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Adicione a dependência a seguir ao nó **Dependencies** . Essa dependência configura um NOP para a fachada de log do Apache [SLF4J](https://www.slf4j.org/) , que é usada pelo SDK do cliente do dispositivo para implementar o registro em log. Essa configuração é opcional, mas, se você omiti-la, poderá ver um aviso no console ao executar o aplicativo. Para obter mais informações sobre o log no SDK do cliente do dispositivo, consulte [Logging](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging) no arquivo Leiame do *SDK do dispositivo IOT do Azure para Java* .

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. Adicione o seguinte nó de **compilação** após o nó de **dependências** . Essa configuração instrui o Maven a usar o Java 1,8 para compilar o aplicativo:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. Salve e feche o arquivo **pom. xml** .

7. Usando um editor de texto, abra o arquivo de origem **Simulated-device\src\main\java\com\mycompany\app\app.java.** .

8. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    ```

9. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. Substitua `{yourdeviceconnectionstring}` pela cadeia de conexão do dispositivo que você anotou na seção [registrar um novo dispositivo no Hub IOT](#register-a-new-device-in-the-iot-hub) :

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

10. Para implementar um manipulador de retorno de chamada para eventos de status do método direto, adicione a seguinte classe aninhada à classe **app** :

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

11. Para implementar um manipulador de retorno de chamada para eventos de status do dispositivo., adicione a seguinte classe aninhada à classe **app** :

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

12. Para implementar um manipulador de retorno de chamada para eventos de propriedade, adicione a seguinte classe aninhada à classe de **aplicativo** :

    ```java
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

13. Para implementar um thread para simular a reinicialização do dispositivo, adicione a seguinte classe aninhada à classe **app** . O thread é suspenso por cinco segundos e, em seguida, define a propriedade relatada **lastReboot** :

    ```java
    protected static class RebootDeviceThread implements Runnable {
      public void run() {
        try {
          System.out.println("Rebooting...");
          Thread.sleep(5000);
          Property property = new Property("lastReboot", LocalDateTime.now());
          Set<Property> properties = new HashSet<Property>();
          properties.add(property);
          client.sendReportedProperties(properties);
          System.out.println("Rebooted");
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

14. Para implementar o método direto no dispositivo, adicione a seguinte classe aninhada à classe **app** . Quando o aplicativo simulado recebe uma chamada para o método direto de reinicialização, ele retorna uma confirmação para o chamador e, em seguida, inicia um thread para processar a reinicialização:

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "reboot" :
          {
            int status = METHOD_SUCCESS;
            System.out.println("Received reboot request");
            deviceMethodData = new DeviceMethodData(status, "Started reboot");
            RebootDeviceThread rebootThread = new RebootDeviceThread();
            Thread t = new Thread(rebootThread);
            t.start();
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

15. Modifique a assinatura do método **principal** para gerar as seguintes exceções:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

16. Para criar uma instância de um **DeviceClient**, substitua o código no método **Main** pelo seguinte código:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

17. Para começar a escutar chamadas de método diretas, adicione o seguinte código ao método **principal** :

    ```java
    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Subscribed to direct methods and polling for reported properties. Waiting...");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

18. Para desligar o simulador de dispositivo, adicione o seguinte código ao método **principal** :

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

19. Guarde e feche o ficheiro simulated-device\src\main\java\com\mycompany\app\app.Java.

20. Crie o aplicativo **Simulated-Device** e corrija os erros. No prompt de comando, navegue até a pasta **Simulated-Device** e execute o seguinte comando:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Executar as aplicações

Agora você está pronto para executar os aplicativos.

1. Em um prompt de comando na pasta **Simulated-Device** , execute o seguinte comando para começar a escutar as chamadas de método de reinicialização do Hub IOT:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplicativo de dispositivo simulado do Hub IoT Java para escutar chamadas de método direto de reinicialização](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. Em um prompt de comando na pasta **Trigger-reboot** , execute o seguinte comando para chamar o método reboot no dispositivo simulado do Hub IOT:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplicativo de serviço do Hub IoT do Java para chamar o método direto de reinicialização](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. O dispositivo simulado responde à chamada de método direto de reinicialização:

    ![O aplicativo de dispositivo simulado do Hub IoT Java responde à chamada de método direto](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
