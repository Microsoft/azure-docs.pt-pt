---
title: Inicie-se com a gestão de dispositivos Azure IoT Hub (Java) / Microsoft Docs
description: Como utilizar a gestão do dispositivo Azure IoT Hub para iniciar um reboot remoto do dispositivo. Utiliza o dispositivo Azure IoT SDK para a Java implementar uma aplicação de dispositivo simulada que inclui um método direto e o serviço Azure IoT SDK para a Java implementar uma aplicação de serviço que invoque o método direto.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt
ms.openlocfilehash: 75d89b54bae6eb8166d44e08ea020a0da67ad20c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732546"
---
# <a name="get-started-with-device-management-java"></a>Começar com a gestão de dispositivos (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Este tutorial mostrar-lhe como:

* Utilize o portal Azure para criar um Hub IoT e criar uma identidade de dispositivo no seu hub IoT.

* Crie uma aplicação simulada de dispositivo que implemente um método direto para reiniciar o dispositivo. Os métodos diretos são invocados da nuvem.

* Crie uma aplicação que invoque o método direto de reinício na aplicação simulada do dispositivo através do seu hub IoT. Esta aplicação monitoriza então as propriedades reportadas do dispositivo para ver quando a operação de reinicialização está concluída.

No final deste tutorial, tens duas aplicações de consola Java:

**dispositivo simulado**. Esta aplicação:

* Conecta-se ao seu hub IoT com a identidade do dispositivo criada anteriormente.

* Recebe uma chamada de método direto de reinicialização.

* Simula um reboot físico.

* Relata a hora do último reboot através de uma propriedade reportada.

reiniciar o **gatilho.** Esta aplicação:

* Chama um método direto na aplicação de dispositivo simulado.

* Apresenta a resposta à chamada de método direto enviada pelo dispositivo simulado.

* Exibe as propriedades reportadas atualizadas.

> [!NOTE]
> Para obter informações sobre os SDKs que pode utilizar para construir aplicações para executar em dispositivos e a sua solução na extremidade traseira, consulte [Os DSKs Azure IoT](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Kit](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)de Desenvolvimento Java SE 8 . Certifique-se de que seleciona **Java 8** sob suporte a **longo prazo** para obter downloads para JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registe um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de ligação do hub IoT

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Desencadear um reboot remoto no dispositivo utilizando um método direto

Nesta secção, cria-se uma aplicação de consola Java que:

1. Invoca o método direto de reinício na aplicação simulada do dispositivo.

2. Mostra a resposta.

3. Sondagens as propriedades reportadas enviadas do dispositivo para determinar quando o reboot está completo.

Esta aplicação de consola conecta-se ao seu IoT Hub para invocar o método direto e ler as propriedades relatadas.

1. Crie uma pasta vazia chamada **dm-get-start.**

2. Na pasta **dm-start-start,** crie um projeto Maven chamado **trigger-reboot** usando o seguinte comando no seu pedido de comando:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. A seu pedido de comando, navegue para a pasta **de arranque do gatilho.**

4. Utilizando um editor de texto, abra o ficheiro **pom.xml** na pasta **de arranque do gatilho** e adicione a seguinte dependência ao nó de **dependências.** Esta dependência permite-lhe utilizar o pacote iot-service-cliente na sua app para comunicar com o seu hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.17.1</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Pode verificar a versão mais recente do **iot-service-client** utilizando a [pesquisa Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

5. Adicione o nó de **construção** seguinte após o nó de **dependências.** Esta configuração instrui Maven a usar Java 1.8 para construir a aplicação:

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

6. Guarde e feche o ficheiro **pom.xml**.

7. Utilizando um editor de texto, abra o **trigger-reboot\src\main\java\com\mycompany\app\app\app\java** source file.

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

9. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. Substitua-a `{youriothubconnectionstring}` com a cadeia de ligação IoT Hub que copiou anteriormente na cadeia de ligação do [hub IoT:](#get-the-iot-hub-connection-string)

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. Para implementar um fio que leia as propriedades reportadas do dispositivo twin a cada 10 segundos, adicione a seguinte classe aninhada à classe **App:**

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

11. Modificar a assinatura do método **principal** para lançar a seguinte exceção:

    ```java
    public static void main(String[] args) throws IOException
    ```

12. Para invocar o método direto de reinício no dispositivo simulado, substitua o código no método **principal** pelo seguinte código:

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

13. Para iniciar o fio para fazer uma pesquisa das propriedades reportadas a partir do dispositivo simulado, adicione o seguinte código ao método **principal:**

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. Para permitir parar a aplicação, adicione o seguinte código ao método **principal:**

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. Guarde e feche o **ficheiro trigger-reboot\src\main\java\com\mycompany\app\app.java.**

16. Construa a aplicação de **back-end de reiniciar** o gatilho e corrija quaisquer erros. A seu pedido de comando, navegue para a pasta **de reiniciar** o gatilho e execute o seguinte comando:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação de dispositivo simulada

Nesta secção, cria-se uma aplicação de consola Java que simula um dispositivo. A aplicação ouve a chamada de método direto reboot do seu hub IoT e responde imediatamente a essa chamada. A aplicação dorme durante algum tempo para simular o processo de reinício antes de usar uma propriedade reportada para notificar a aplicação de **back-end de reiniciar** o gatilho de que o reboot está completo.

1. Na pasta **dm-start-start,** crie um projeto Maven chamado **dispositivo simulado** utilizando o seguinte comando no seu pedido de comando:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. A seu pedido de comando, navegue para a pasta **do dispositivo simulado.**

3. Utilizando um editor de texto, abra o ficheiro **pom.xml** na pasta **do dispositivo simulado** e adicione a seguinte dependência ao nó de **dependências.** Esta dependência permite-lhe utilizar o pacote iot-service-cliente na sua app para comunicar com o seu hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Pode verificar a versão mais recente do **iot-device-client** utilizando a [pesquisa Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Adicione a seguinte dependência ao nó de **dependências.** Esta dependência configura um NOP para a fachada de exploração de apache [SLF4J,](https://www.slf4j.org/) que é usada pelo cliente do dispositivo SDK para implementar a exploração madeireira. Esta configuração é opcional, mas, se a omitir, poderá ver um aviso na consola quando executar a aplicação. Para obter mais informações sobre o login no cliente do dispositivo SDK, consulte [o Registo de Registos](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging) nas *Amostras para o dispositivo Azure IoT SDK para java* readme file.

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. Adicione o nó de **construção** seguinte após o nó de **dependências.** Esta configuração instrui Maven a usar Java 1.8 para construir a aplicação:

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

6. Guarde e feche o ficheiro **pom.xml**.

7. Utilizando um editor de texto, abra o ficheiro de fonte do **dispositivo simulado\src\java\com\mycompany\app\app\.**

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

9. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. Substitua-a `{yourdeviceconnectionstring}` pela cadeia de ligação do dispositivo que observou no Registo um novo dispositivo na secção [ioT:](#register-a-new-device-in-the-iot-hub)

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

10. Para implementar um manipulador de chamadas para eventos de estado de método direto, adicione a seguinte classe aninhada à classe **App:**

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

11. Para implementar um manipulador de chamadas para eventos de estado gémeo do dispositivo, adicione a seguinte classe aninhada à classe **App:**

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

12. Para implementar um manipulador de chamadas para eventos imobiliários, adicione a seguinte classe aninhada à classe **App:**

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

13. Para implementar um fio para simular o reboot do dispositivo, adicione a seguinte classe aninhada à classe **App.** O fio dorme durante cinco segundos e, em seguida, define a última propriedade reportada **reboot:**

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

14. Para implementar o método direto no dispositivo, adicione a seguinte classe aninhada à classe **App.** Quando a aplicação simulada recebe uma chamada para **o** método direct reboot, devolve um reconhecimento ao chamador e, em seguida, inicia um fio para processar o reboot:

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

15. Modificar a assinatura do método **principal** para lançar as seguintes exceções:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

16. Para instantaneamente um **DeviceClient,** substitua o código no método **principal** com o seguinte código:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

17. Para começar a ouvir chamadas de métodos diretos, adicione o seguinte código ao método **principal:**

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

18. Para desligar o simulador do dispositivo, adicione o seguinte código ao método **principal:**

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

19. Guarde e feche o ficheiro simulado\src\main\java\com\mycompany\app\app\app.java.

20. Construa a aplicação **de dispositivo simulado** e corrija quaisquer erros. A seu pedido de comando, navegue para a pasta **do dispositivo simulado** e execute o seguinte comando:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Executar as aplicações

Está pronto para executar as aplicações.

1. Num pedido de comando na pasta **do dispositivo simulado,** execute o seguinte comando para começar a ouvir chamadas de método de reinicialização do seu hub IoT:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT Hub simulado app de dispositivos para ouvir chamadas de método sinuoso](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. Num pedido de comando na pasta **de arranque do gatilho,** execute o seguinte comando para ligar para o método de reinicialização do seu dispositivo simulado a partir do seu hub IoT:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![App de serviço Java IoT Hub para chamar o método direto de reboot](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. O dispositivo simulado responde à chamada do método de reinicialização:

    ![App simulada java IoT Hub responde à chamada de método direto](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
