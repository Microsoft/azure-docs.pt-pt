---
title: Agendar tarefas com o IoT Hub do Azure (Java) | Documentos da Microsoft
description: Como agendar um trabalho do IoT Hub do Azure para invocar um método direto e definir uma propriedade pretendida em vários dispositivos. Utilizar o Azure IoT device SDK para Java para implementar as aplicações de dispositivo simulado e o serviço de IoT do Azure SDK para Java para implementar uma aplicação de serviço para executar a tarefa.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 07/10/2017
ms.openlocfilehash: d2d92c05e54bc78d5b01e73b586e5c25fc431255
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011403"
---
# <a name="schedule-and-broadcast-jobs-java"></a>Agendar e difundir tarefas (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Utilize o IoT Hub do Azure para agendar e monitorizar tarefas que atualizar milhões de dispositivos. Utilize tarefas para:

* Atualizar as propriedades pretendidas
* Etiquetas de atualização
* Invocar métodos diretos

Uma tarefa encapsula uma destas ações e controla a execução com um conjunto de dispositivos. Uma consulta de twin do dispositivo define o conjunto de dispositivos, que a tarefa é executado em relação. Por exemplo, uma aplicação de back-end pode utilizar uma tarefa para invocar um método direto num 10 000 dispositivos que reinicia os dispositivos. Especifique o conjunto de dispositivos com uma consulta do dispositivo duplo e agendar a tarefa seja executada num período futuro. O progresso da tarefa roteiros como cada um dos dispositivos receber e executar o método direto de reinício.

Para saber mais sobre cada uma destas capacidades, consulte:

* Dispositivo duplo e propriedades: [Introdução aos dispositivos duplos](iot-hub-java-java-twin-getstarted.md)

* Métodos diretos: [Guia do programador do IoT Hub - métodos diretos](iot-hub-devguide-direct-methods.md) e [Tutorial: Utilizar métodos diretos](quickstart-control-device-java.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial mostrar-lhe como:

* Criar uma aplicação de dispositivo que implementa um método direto chamado **lockDoor**. A aplicação de dispositivo também recebe as alterações de propriedade pretendida a partir da aplicação de back-end.

* Criar uma aplicação de back-end que cria uma tarefa para chamar o **lockDoor** método direto em vários dispositivos. Outra tarefa envia atualizações de propriedade pretendida para vários dispositivos.

No final deste tutorial, terá de uma aplicação de dispositivo da consola de java e uma aplicação de back-end de consola java:

**simulated-device** que liga ao seu hub IoT, implementa o **lockDoor** direcionar o método e identificadores pretendido alterações de propriedade.

**tarefas de agendamento** que usar tarefas para chamar o **lockDoor** método direto e atualizar as propriedades dos dispositivos duplos desejado em vários dispositivos.

> [!NOTE]
> O artigo [do Azure IoT SDKs](iot-hub-devguide-sdks.md) fornece informações sobre os SDKs IoT do Azure que pode utilizar para criar aplicações de dispositivos e back-end.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* A versão mais recente de [Java SE Development Kit 8](https://aka.ms/azure-jdks)

* [Maven 3](https://maven.apache.org/install.html)

* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Obter cadeia de ligação do IoT hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registar um novo dispositivo no IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

Também pode utilizar o [extensão de IoT para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension) ferramenta para adicionar um dispositivo ao seu hub IoT.

## <a name="create-the-service-app"></a>Criar a aplicação de serviço

Nesta secção, vai criar uma aplicação de consola Java que utiliza as tarefas:

* Chamar o **lockDoor** método direto em vários dispositivos.

* Envie as propriedades pretendidas para vários dispositivos.

Para criar a aplicação:

1. No computador de desenvolvimento, crie uma pasta vazia designada `iot-java-schedule-jobs`.

2. Na `iot-java-schedule-jobs` pasta, crie um projeto Maven designado **programar tarefas** com o seguinte comando na sua linha de comandos. Tenha em atenção que se trata de um comando único, por extenso:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

3. Na linha de comandos, navegue para o `schedule-jobs` pasta.

4. Com um editor de texto, abra a `pom.xml` de ficheiros a `schedule-jobs` pasta e adicione a seguinte dependência à **dependências** nó. Esta dependência permite-lhe utilizar o **iot-service-client** pacote na sua aplicação para comunicar com IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Pode verificar a versão mais recente do **cliente do serviço iot** usando [pesquisa Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

5. Adicione as seguintes **crie** nó após a **dependências** nó. Esta configuração instrui o Maven para utilizar o Java 1.8 para criar a aplicação:

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

6. Guarde e feche o `pom.xml` ficheiro.

7. Com um editor de texto, abra o `schedule-jobs\src\main\java\com\mycompany\app\App.java` ficheiro.

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

9. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. Substitua `{youriothubconnectionstring}` com a cadeia de ligação do hub de IoT que anotou no *criar um IoT Hub* secção:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

10. Adicione o seguinte método para o **App** classe para agendar uma tarefa para atualizar a **modulares** e **piso** propriedades no dispositivo duplo pretendidas:

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

11. Agendar um trabalho para chamar o **lockDoor** método, adicione o seguinte método para o **aplicação** classe:

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

12. Para monitorizar um trabalho, adicione o seguinte método para o **aplicação** classe:

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

13. Para consultar os detalhes das tarefas de que executou, adicione o seguinte método:

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

14. Atualização do **principal** assinatura do método para incluir o seguinte `throws` cláusula:

    ```java
    public static void main( String[] args ) throws Exception
    ```

15. Para executar e monitorizar as duas tarefas em seqüência, adicione o seguinte código para o **principal** método:

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

16. Guarde e feche o `schedule-jobs\src\main\java\com\mycompany\app\App.java` ficheiro

17. Criar a **programar tarefas** aplicação e corrigir eventuais erros. Na linha de comandos, navegue para o `schedule-jobs` pasta e execute o seguinte comando:

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>Criar uma aplicação de dispositivo

Nesta secção, vai criar uma aplicação de consola do Java que lida com as propriedades pretendidas enviadas a partir do IoT Hub e implementa a chamada de método direto.

1. Na `iot-java-schedule-jobs` pasta, crie um projeto Maven designado **simulated-device** com o seguinte comando na sua linha de comandos. Tenha em atenção que se trata de um comando único, por extenso:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

2. Na linha de comandos, navegue para o `simulated-device` pasta.

3. Com um editor de texto, abra a `pom.xml` de ficheiros a `simulated-device` pasta e adicione as seguintes dependências para o **dependências** nó. Esta dependência permite-lhe utilizar o **iot-device-client** pacote na sua aplicação para comunicar com IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Pode verificar a versão mais recente do **cliente do dispositivo iot** usando [pesquisa Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Adicione as seguintes **crie** nó após a **dependências** nó. Esta configuração instrui o Maven para utilizar o Java 1.8 para criar a aplicação:

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

5. Guarde e feche o `pom.xml` ficheiro.

6. Com um editor de texto, abra o `simulated-device\src\main\java\com\mycompany\app\App.java` ficheiro.

7. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

8. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. Substituindo `{youriothubname}` com o nome do seu IoT hub e `{yourdevicekey}` com a chave de dispositivo valor que gerou na *criar uma identidade de dispositivo* secção:

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Esta aplicação de exemplo utiliza a variável de **protocolo** para instanciar um objeto **DeviceClient**.

9. Para imprimir notificações de twin do dispositivo para a consola, adicione a seguinte classe aninhada para o **aplicação** classe:

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

10. Para imprimir notificações de método direto para a consola, adicione a seguinte classe aninhada para o **aplicação** classe:

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

11. Para processar chamadas de método direto do IoT Hub, adicione a seguinte classe aninhada para o **aplicação** classe:

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

12. Atualização do **principal** assinatura do método para incluir o seguinte `throws` cláusula:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

13. Adicione o seguinte código para o **principal** método para:
    * Crie um cliente de dispositivo para comunicar com IoT Hub.
    * Criar uma **dispositivo** objeto para armazenar propriedades de twin do dispositivo.

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

14. Para iniciar os serviços de cliente do dispositivo, adicione o seguinte código para o **principal** método:

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

15. Aguardar que o utilizador prima a **Enter** chave antes de encerrar, adicione o seguinte código ao final do **principal** método:

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

16. Guarde e feche o `simulated-device\src\main\java\com\mycompany\app\App.java` ficheiro.

17. Criar a **simulated-device** aplicação e corrigir eventuais erros. Na linha de comandos, navegue para o `simulated-device` pasta e execute o seguinte comando:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Executar as aplicações

Agora está pronto para executar as aplicações de consola.

1. Uma linha de comandos a `simulated-device` pasta, execute o seguinte comando para iniciar a aplicação de dispositivo à escuta de alterações de propriedade pretendida e chamadas de método direto:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![O cliente de dispositivo é iniciado](./media/iot-hub-java-java-schedule-jobs/device-app-1.png)

2. Uma linha de comandos do `schedule-jobs` pasta, execute o seguinte comando para executar o **programar tarefas** do serviço de aplicações para executar duas tarefas. O primeiro define os valores de propriedade pretendida, o segundo chama o método direto:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Aplicação de serviço do Java IoT Hub cria duas tarefas](./media/iot-hub-java-java-schedule-jobs/service-app-1.png)

3. O aplicativo de dispositivo manipula a alteração de propriedade pretendida e a chamada de método direto:

    ![O cliente de dispositivo responde às alterações](./media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Criou uma aplicação de back-end para executar duas tarefas. A primeira tarefa definir valores de propriedade pretendida e a segunda tarefa chama um método direto.

Utilize os seguintes recursos para saber como:

* Enviar telemetria a partir de dispositivos com o [introdução ao IoT Hub](quickstart-send-telemetry-java.md) tutorial.

* Controlar dispositivos interativamente (como ativar um fã de uma aplicação controlada pelo utilizador) com o [utilizar métodos diretos](quickstart-control-device-java.md) tutorial.s