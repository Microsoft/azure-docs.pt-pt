---
title: Agendar empregos com a Azure IoT Hub (Java) | Microsoft Docs
description: Como agendar um trabalho do Azure IoT Hub para invocar um método direto e definir uma propriedade desejada em vários dispositivos. Utiliza o dispositivo Azure IoT SDK para a Java para implementar as aplicações simuladas do dispositivo e o serviço Azure IoT SDK para a Java implementar uma aplicação de serviço para executar o trabalho.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt, devx-track-java
ms.openlocfilehash: 3e98cfc2d8c7fb8d40c8565a1c620f123ce171ff
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102217844"
---
# <a name="schedule-and-broadcast-jobs-java"></a>Horários e trabalhos de transmissão (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Use o Azure IoT Hub para agendar e rastrear trabalhos que atualizam milhões de dispositivos. Utilizar empregos para:

* Atualizar as propriedades pretendidas
* Etiquetas de atualização
* Invocar métodos diretos

Um trabalho envolve uma destas ações e rastreia a execução contra um conjunto de dispositivos. Uma consulta dupla do dispositivo define o conjunto de dispositivos contra os quais o trabalho executa. Por exemplo, uma aplicação back-end pode usar um trabalho para invocar um método direto em 10.000 dispositivos que reiniciam os dispositivos. Especifica o conjunto de dispositivos com uma consulta dupla do dispositivo e agenda o trabalho para ser executado num futuro próximo. O trabalho acompanha o progresso à medida que cada um dos dispositivos recebe e executa o método direto de reinicialização.

Para saber mais sobre cada uma destas capacidades, consulte:

* Dispositivo twin e propriedades: [Começar com os gémeos do dispositivo](iot-hub-java-java-twin-getstarted.md)

* Métodos diretos: [Guia de desenvolvimento do IoT Hub - métodos diretos](iot-hub-devguide-direct-methods.md) e [tutorial: Utilize métodos diretos](quickstart-control-device-java.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial mostrar-lhe como:

* Crie uma aplicação para dispositivos que implemente um método direto chamado **lockDoor**. A aplicação do dispositivo também recebe as alterações de propriedade desejadas a partir da aplicação back-end.

* Crie uma aplicação back-end que cria um trabalho para ligar para o método direto **lockDoor** em vários dispositivos. Outro trabalho envia atualizações de propriedade desejadas para vários dispositivos.

No final deste tutorial, tem uma aplicação para dispositivos java consola e uma aplicação de back-end java consola:

**dispositivo simulado** que se conecta ao seu hub IoT, implementa o método direto **lockDoor** e lida com as alterações de propriedade desejadas.

**agendar trabalhos** que usam trabalhos para ligar para o método direto **lockDoor** e atualizar as propriedades desejadas pelo dispositivo twin em vários dispositivos.

> [!NOTE]
> O artigo [Azure IoT SDKs](iot-hub-devguide-sdks.md) fornece informações sobre os SDKs Azure IoT que você pode usar para construir tanto dispositivos como aplicações de back-end.

## <a name="prerequisites"></a>Pré-requisitos

* [Kit de Desenvolvimento Java SE 8](/java/azure/jdk/). Certifique-se de que seleciona **Java 8** sob **suporte a longo prazo** para obter downloads para JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

Também pode utilizar a [extensão IoT para](https://github.com/Azure/azure-iot-cli-extension) a ferramenta Azure CLI para adicionar um dispositivo ao seu hub IoT.

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de conexão do hub IoT

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-the-service-app"></a>Criar a app de serviço

Nesta secção, cria-se uma aplicação java para consolas que utiliza empregos para:

* Ligue para o método de **bloqueioDoor** em vários dispositivos.

* Envie propriedades desejadas para vários dispositivos.

Para criar a aplicação:

1. Na sua máquina de desenvolvimento, crie uma pasta vazia chamada **iot-java-agenda-jobs**.

2. Na pasta **iot-java-agenda-jobs,** crie um projeto Maven chamado **agenda-jobs** usando o seguinte comando no seu comando. Tenha em atenção que se trata de um comando único, por extenso:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

3. No seu comando, navegue para a pasta **de trabalhos de agendamento.**

4. Utilizando um editor de texto, abra o ficheiro **pom.xml** na pasta **de trabalhos de agendamento** e adicione a seguinte dependência ao nó de **dependências.** Esta dependência permite-lhe utilizar o pacote **iot-service-cliente** na sua app para comunicar com o seu hub IoT:

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

5. Adicione o nó **de construção** seguinte após o nó **de dependências.** Esta configuração instrui Maven a usar Java 1.8 para construir a aplicação:

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

7. Utilizando um editor de texto, abra o **ficheiro de horários\src\main\java\com\mycompany\app\App.java** ficheiro.

8. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Pair;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Query;
    import com.microsoft.azure.sdk.iot.service.devicetwin.SqlQuery;
    import com.microsoft.azure.sdk.iot.service.jobs.JobClient;
    import com.microsoft.azure.sdk.iot.service.jobs.JobResult;
    import com.microsoft.azure.sdk.iot.service.jobs.JobStatus;

    import java.util.Date;
    import java.time.Instant;
    import java.util.HashSet;
    import java.util.Set;
    import java.util.UUID;
    ```

9. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. `{youriothubconnectionstring}`Substitua-a pela sua cadeia de ligação do hub IoT que copiou anteriormente na cadeia de [ligação do hub IoT](#get-the-iot-hub-connection-string):

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

10. Adicione o seguinte método à classe **App** para agendar um trabalho para atualizar as propriedades desejadas do **Edifício** e **do Piso** no dispositivo twin:

    ```java
    private static JobResult scheduleJobSetDesiredProperties(JobClient jobClient, String jobId) {
      DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
      Set<Pair> desiredProperties = new HashSet<Pair>();
      desiredProperties.add(new Pair("Building", 43));
      desiredProperties.add(new Pair("Floor", 3));
      twin.setDesiredProperties(desiredProperties);
      // Optimistic concurrency control
      twin.setETag("*");

      // Schedule the update twin job to run now
      // against a single device
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleUpdateTwin(jobId, 
          "deviceId='" + deviceId + "'",
          twin,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling desired properties job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    }
    ```

11. Para agendar um trabalho para ligar para o método **lockDoor,** adicione o seguinte método à classe **App:**

    ```java
    private static JobResult scheduleJobCallDirectMethod(JobClient jobClient, String jobId) {
      // Schedule a job now to call the lockDoor direct method
      // against a single device. Response and connection
      // timeouts are set to 5 seconds.
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleDeviceMethod(jobId,
          "deviceId='" + deviceId + "'",
          "lockDoor",
          5L, 5L, null,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling direct method job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    };
    ```

12. Para monitorizar um trabalho, adicione o seguinte método à classe **App:**

    ```java
    private static void monitorJob(JobClient jobClient, String jobId) {
      try {
        JobResult jobResult = jobClient.getJob(jobId);
        if(jobResult == null)
        {
          System.out.println("No JobResult for: " + jobId);
          return;
        }
        // Check the job result until it's completed
        while(jobResult.getJobStatus() != JobStatus.completed)
        {
          Thread.sleep(100);
          jobResult = jobClient.getJob(jobId);
          System.out.println("Status " + jobResult.getJobStatus() + " for job " + jobId);
        }
        System.out.println("Final status " + jobResult.getJobStatus() + " for job " + jobId);
      } catch (Exception e) {
        System.out.println("Exception monitoring job: " + jobId);
        System.out.println(e.getMessage());
        return;
      }
    }
    ```

13. Para consultar os detalhes dos trabalhos que executou, adicione o seguinte método:

    ```java
    private static void queryDeviceJobs(JobClient jobClient, String start) throws Exception {
      System.out.println("\nQuery device jobs since " + start);

      // Create a jobs query using the time the jobs started
      Query deviceJobQuery = jobClient
          .queryDeviceJob(SqlQuery.createSqlQuery("*", SqlQuery.FromType.JOBS, "devices.jobs.startTimeUtc > '" + start + "'", null).getQuery());

      // Iterate over the list of jobs and print the details
      while (jobClient.hasNextJob(deviceJobQuery)) {
        System.out.println(jobClient.getNextJob(deviceJobQuery));
      }
    }
    ```

14. Atualizar a assinatura do método **principal** para incluir a seguinte `throws` cláusula:

    ```java
    public static void main( String[] args ) throws Exception
    ```

15. Para executar e monitorizar dois postos de trabalho sequencialmente, substitua o código no método **principal** pelo seguinte código:

    ```java
    // Record the start time
    String start = Instant.now().toString();

    // Create JobClient
    JobClient jobClient = JobClient.createFromConnectionString(iotHubConnectionString);
    System.out.println("JobClient created with success");

    // Schedule twin job desired properties
    // Maximum concurrent jobs is 1 for Free and S1 tiers
    String desiredPropertiesJobId = "DPCMD" + UUID.randomUUID();
    scheduleJobSetDesiredProperties(jobClient, desiredPropertiesJobId);
    monitorJob(jobClient, desiredPropertiesJobId);

    // Schedule twin job direct method
    String directMethodJobId = "DMCMD" + UUID.randomUUID();
    scheduleJobCallDirectMethod(jobClient, directMethodJobId);
    monitorJob(jobClient, directMethodJobId);

    // Run a query to show the job detail
    queryDeviceJobs(jobClient, start);

    System.out.println("Shutting down schedule-jobs app");
    ```

16. Guardar e fechar o **programa-trabalhos\src\main\java\com\mycompany\app\App.java** ficheiro

17. Construa a aplicação **de trabalhos de agendamento** e corrija quaisquer erros. No seu comando, navegue para a pasta **de trabalhos de agendamento** e execute o seguinte comando:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Criar uma aplicação de dispositivo

Nesta secção, cria-se uma aplicação de consola Java que trata das propriedades desejadas enviadas a partir do IoT Hub e implementa a chamada de método direto.

1. Na pasta **iot-java-agenda-jobs,** crie um projeto Maven chamado **dispositivo simulado** usando o seguinte comando no seu comando. Tenha em atenção que se trata de um comando único, por extenso:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

2. No seu comando, navegue para a pasta **do dispositivo simulado.**

3. Utilizando um editor de texto, abra o ficheiro **pom.xml** na pasta do **dispositivo simulado** e adicione as seguintes dependências ao nó de **dependências.** Esta dependência permite-lhe utilizar o pacote **iot-dispositivo-cliente** na sua app para comunicar com o seu hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Pode verificar a versão mais recente do **iot-device-client** utilizando a [pesquisa Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Adicione a seguinte dependência ao nó **de dependências.** Esta dependência configura um NOP para a fachada de registo Apache [SLF4J,](https://www.slf4j.org/) que é usada pelo cliente do dispositivo SDK para implementar a exploração madeireira. Esta configuração é opcional, mas se a omitir, poderá ver um aviso na consola quando executar a aplicação. Para obter mais informações sobre o registo no cliente do dispositivo SDK, consulte [registar-se](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging)nas *amostras do ficheiro SDK do dispositivo Azure IoT para* o ficheiro de leitura Java.

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. Adicione o nó **de construção** seguinte após o nó **de dependências.** Esta configuração instrui Maven a usar Java 1.8 para construir a aplicação:

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

7. Utilizando um editor de texto, abra o **ficheiro simulado\src\main\java\com\mycompany\app\App\App.java** ficheiro.

8. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

9. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. `{yourdeviceconnectionstring}`Substitua-a pela cadeia de ligação do dispositivo que copiou anteriormente no Registo de um novo dispositivo na secção [hub IoT:](#register-a-new-device-in-the-iot-hub)

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Esta aplicação de exemplo utiliza a variável de **protocolo** para instanciar um objeto **DeviceClient**.

10. Para imprimir notificações duplas do dispositivo à consola, adicione a seguinte classe aninhada à classe **App:**

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

11. Para imprimir notificações de métodos diretos para a consola, adicione a seguinte classe aninhada à classe **App:**

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

12. Para lidar com chamadas de métodos diretos do IoT Hub, adicione a seguinte classe aninhada à classe **App:**

    ```java
    // Handler for direct method calls from IoT Hub
    protected static class DirectMethodCallback
        implements DeviceMethodCallback {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context) {
        DeviceMethodData deviceMethodData;
        switch (methodName) {
          case "lockDoor": {
            System.out.println("Executing direct method: " + methodName);
            deviceMethodData = new DeviceMethodData(METHOD_SUCCESS, "Executed direct method " + methodName);
            break;
          }
          default: {
            deviceMethodData = new DeviceMethodData(METHOD_NOT_DEFINED, "Not defined direct method " + methodName);
          }
        }
        // Notify IoT Hub of result
        return deviceMethodData;
      }
    }
    ```

13. Atualizar a assinatura do método **principal** para incluir a seguinte `throws` cláusula:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

14. Substitua o código no método **principal** pelo seguinte código para:
    * Crie um cliente de dispositivo para comunicar com o IoT Hub.
    * Crie um objeto **do Dispositivo** para armazenar as propriedades gémeas do dispositivo.

    ```java
    // Create a device client
    DeviceClient client = new DeviceClient(connString, protocol);

    // An object to manage device twin desired and reported properties
    Device dataCollector = new Device() {
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context)
      {
        System.out.println("Received desired property change: " + propertyKey + " " + propertyValue);
      }
    };
    ```

15. Para iniciar os serviços do cliente do dispositivo, adicione o seguinte código ao método **principal:**

    ```java
    try {
      // Open the DeviceClient
      // Start the device twin services
      // Subscribe to direct method calls
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
    } catch (Exception e) {
      System.out.println("Exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

16. Para esperar que o utilizador prima a tecla **'Introduzir'** antes de desligar, adicione o seguinte código ao fim do método **principal:**

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

17. Guarde e feche o **ficheiro simulado\src\main\java\com\mycompany\app\App.java** ficheiro.

18. Construa a aplicação **de dispositivo simulado** e corrija quaisquer erros. No seu comando, navegue para a pasta do **dispositivo simulado** e execute o seguinte comando:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Executar as aplicações

Está agora pronto para executar as aplicações da consola.

1. Numa localização de comando na pasta do **dispositivo simulado,** executar o seguinte comando para iniciar a aplicação do dispositivo ouvindo as alterações de propriedade desejadas e chamadas de métodos diretos:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![O cliente do dispositivo começa](./media/iot-hub-java-java-schedule-jobs/device-app-1.png)

2. Com um pedido de comando na `schedule-jobs` pasta, executar o seguinte comando para executar a app de serviço **de trabalhos de agenda** para executar dois empregos. O primeiro define os valores de propriedade desejados, o segundo chama o método direto:

   ```cmd\sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![App de serviço Java IoT Hub cria dois empregos](./media/iot-hub-java-java-schedule-jobs/service-app-1.png)

3. A aplicação do dispositivo trata da alteração de propriedade desejada e da chamada de método direto:

   ![O cliente do dispositivo responde às alterações](./media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, utilizou um trabalho para agendar um método direto para um dispositivo e a atualização das propriedades do dispositivo Twin.

Utilize os seguintes recursos para aprender a:

* Envie telemetria a partir de dispositivos com o Tutorial Get start com o tutorial [IoT Hub.](quickstart-send-telemetry-java.md)

* Dispositivos de controlo interativamente (como ligar um ventilador de uma aplicação controlada pelo utilizador) com o [tutorial de métodos diretos Use](quickstart-control-device-java.md)