---
title: Introdução aos duplos de dispositivo do IoT Hub do Azure (Java) | Documentos da Microsoft
description: Como utilizar o gémeos de dispositivo do IoT Hub do Azure para adicionar etiquetas e, em seguida, utilizar uma consulta do IoT Hub. Utilizar o Azure IoT device SDK para Java para implementar a aplicação de dispositivo e o serviço de IoT do Azure SDK para Java para implementar uma aplicação de serviço que adiciona as etiquetas e executa a consulta do IoT Hub.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 07/04/2017
ms.openlocfilehash: bfb111b07db105190fc59f21b3255c2ea2b1471c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58081020"
---
# <a name="get-started-with-device-twins-java"></a>Introdução aos dispositivos duplos (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Neste tutorial, vai criar duas aplicações de consola Java:

* **Adicionar etiquetas-consulta**, uma aplicação de back-end de Java que adiciona as etiquetas e consultas de gémeos de dispositivo.
* **simulated-device**, uma aplicação de dispositivo de Java que liga ao seu hub IoT e reporta sua condição de conectividade com uma propriedade comunicada.

> [!NOTE]
> O artigo [do Azure IoT SDKs](iot-hub-devguide-sdks.md) fornece informações sobre os SDKs IoT do Azure que pode utilizar para criar aplicações de dispositivos e back-end.

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

## <a name="create-the-service-app"></a>Criar a aplicação de serviço

Nesta secção, vai criar uma aplicação Java que adiciona os metadados de localização, como uma etiqueta para o dispositivo duplo no IoT Hub associado **myDeviceId**. A aplicação consulta pela primeira vez o hub IoT para dispositivos localizados nos Estados Unidos e, em seguida, para dispositivos que reportam uma ligação de rede celular.

1. No computador de desenvolvimento, crie uma pasta vazia designada `iot-java-twin-getstarted`.

2. Na `iot-java-twin-getstarted` pasta, crie um projeto Maven designado **adicionar marcas-consulta** com o seguinte comando na sua linha de comandos. Tenha em atenção que se trata de um comando único, por extenso:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. Na linha de comandos, navegue para o `add-tags-query` pasta.

4. Com um editor de texto, abra a `pom.xml` de ficheiros a `add-tags-query` pasta e adicione a seguinte dependência à **dependências** nó. Esta dependência permite-lhe utilizar o **iot-service-client** pacote na sua aplicação para comunicar com IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Pode verificar a versão mais recente do **cliente do serviço iot** usando [pesquisa Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

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

7. Com um editor de texto, abra o `add-tags-query\src\main\java\com\mycompany\app\App.java` ficheiro.

8. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

9. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. Substitua `{youriothubconnectionstring}` com a cadeia de ligação do hub de IoT que anotou no *criar um IoT Hub* secção:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

10. Atualização do **principal** assinatura do método para incluir o seguinte `throws` cláusula:

    ```java
    public static void main( String[] args ) throws IOException
    ```

11. Adicione o seguinte código para o **principal** método para criar o **DeviceTwin** e **DeviceTwinDevice** objetos. O **DeviceTwin** objeto processa a comunicação com o seu hub IoT. O **DeviceTwinDevice** objeto representa o dispositivo duplo com suas propriedades e etiquetas:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

12. Adicione as seguintes `try/catch` bloquear para o **principal** método:

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

13. Para atualizar o **região** e **fábrica** etiquetas de gémeos de dispositivo no seu dispositivo duplo, adicione o seguinte código no `try` bloco:

    ```java
    // Get the device twin from IoT Hub
    System.out.println("Device twin before update:");
    twinClient.getTwin(device);
    System.out.println(device);

    // Update device twin tags if they are different
    // from the existing values
    String currentTags = device.tagsToString();
    if ((!currentTags.contains("region=" + region) && !currentTags.contains("plant=" + plant))) {
      // Create the tags and attach them to the DeviceTwinDevice object
      Set<Pair> tags = new HashSet<Pair>();
      tags.add(new Pair("region", region));
      tags.add(new Pair("plant", plant));
      device.setTags(tags);

      // Update the device twin in IoT Hub
      System.out.println("Updating device twin");
      twinClient.updateTwin(device);
    }

    // Retrieve the device twin with the tag values from IoT Hub
    System.out.println("Device twin after update:");
    twinClient.getTwin(device);
    System.out.println(device);
    ```

14. Para consultar os dispositivos duplos no IoT hub, adicione o seguinte código para o `try` bloco depois do código adicionado no passo anterior. O código executa duas consultas. Cada consulta retorna um máximo de 100 dispositivos:

    ```java
    // Query the device twins in IoT Hub
    System.out.println("Devices in Redmond:");

    // Construct the query
    SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43'", null);

    // Run the query, returning a maximum of 100 devices
    Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }

    System.out.println("Devices in Redmond using a cellular network:");

    // Construct the query
    sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43' AND properties.reported.connectivityType = 'cellular'", null);

    // Run the query, returning a maximum of 100 devices
    twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 3);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }
    ```

15. Guarde e feche o `add-tags-query\src\main\java\com\mycompany\app\App.java` ficheiro

16. Criar a **adicionar marcas-consulta** aplicação e corrigir eventuais erros. Na linha de comandos, navegue para o `add-tags-query` pasta e execute o seguinte comando:

    ```
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Criar uma aplicação de dispositivo

Nesta secção, vai criar uma aplicação de consola do Java que define um valor de propriedade comunicada, que é enviado para o IoT Hub.

1. Na `iot-java-twin-getstarted` pasta, crie um projeto Maven designado **simulated-device** com o seguinte comando na sua linha de comandos. Tenha em atenção que se trata de um comando único, por extenso:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Na linha de comandos, navegue para o `simulated-device` pasta.

3. Com um editor de texto, abra a `pom.xml` de ficheiros a `simulated-device` pasta e adicione as seguintes dependências para o **dependências** nó. Esta dependência permite-lhe utilizar o **iot-device-client** pacote na sua aplicação para comunicar com IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.14.2</version>
    </dependency>
    ```

    > [!NOTE]
    > Pode verificar a versão mais recente do **cliente do dispositivo iot** usando [pesquisa Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

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
    private static String deviceId = "myDeviceId";
    ```

    Esta aplicação de exemplo utiliza a variável de **protocolo** para instanciar um objeto **DeviceClient**. 

1. Adicione o seguinte método para o **aplicação** classe para imprimir informações sobre atualizações de duplo:

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
          System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
      }
    ```

9. Adicione o seguinte código para o **principal** método para:
    * Crie um cliente de dispositivo para comunicar com IoT Hub.
    * Criar uma **dispositivo** objeto para armazenar propriedades de twin do dispositivo.

      ```java
      DeviceClient client = new DeviceClient(connString, protocol);

      // Create a Device object to store the device twin properties
      Device dataCollector = new Device() {
      // Print details when a property value changes
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context) {
        System.out.println(propertyKey + " changed to " + propertyValue);
      }
      };
      ```

10. Adicione o seguinte código para o **principal** método para criar um **connectivityType** comunicado propriedade e enviá-lo para o IoT Hub:

    ```java
    try {
      // Open the DeviceClient and start the device twin services.
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);

      // Create a reported property and send it to your IoT hub.
      dataCollector.setReportedProp(new Property("connectivityType", "cellular"));
      client.sendReportedProperties(dataCollector.getReportedProp());
    }
    catch (Exception e) {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

11. Adicione o seguinte código ao final dos **principal** método. A aguardar que o **Enter** chave permite tempo para o IoT Hub comunicar o estado das operações de twin do dispositivo:

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

1. Modifique a assinatura do método **principal** para incluir as exceções da seguinte forma:

     ```java
     public static void main(String[] args) throws URISyntaxException, IOException
     ```

1. Guarde e feche o `simulated-device\src\main\java\com\mycompany\app\App.java` ficheiro.

13. Criar a **simulated-device** aplicação e corrigir eventuais erros. Na linha de comandos, navegue para o `simulated-device` pasta e execute o seguinte comando:

    ```
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Executar as aplicações

Agora está pronto para executar as aplicações de consola.

1. Uma linha de comandos do `add-tags-query` pasta, execute o seguinte comando para executar o **adicionar marcas-consulta** do serviço de aplicações:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplicação de serviço do Java IoT Hub para atualizar os valores de etiqueta e executar consultas de dispositivo](./media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    Pode ver o **fábrica** e **região** marcas adicionadas para o dispositivo duplo. A primeira consulta devolve o seu dispositivo, mas o segundo não.

2. Uma linha de comandos do `simulated-device` pasta, execute o seguinte comando para adicionar a **connectivityType** comunicado propriedade para o dispositivo duplo:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![O cliente de dispositivo adiciona a * * connectivityType * * comunicado propriedade](./media/iot-hub-java-java-twin-getstarted/device-app-1.png)

3. Uma linha de comandos do `add-tags-query` pasta, execute o seguinte comando para executar o **adicionar marcas-consulta** uma segunda vez do serviço de aplicações:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplicação de serviço do Java IoT Hub para atualizar os valores de etiqueta e executar consultas de dispositivo](./media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Agora seu dispositivo enviou a **connectivityType** propriedade para o IoT Hub, a segunda consulta devolve o seu dispositivo.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Adicionados metadados do dispositivo como etiquetas a partir de uma aplicação de back-end e escreveu uma aplicação de dispositivo para informações de conectividade do dispositivo de relatório no dispositivo duplo. Também aprendeu como consultar as informações do dispositivo duplo usando a linguagem de consulta do Hub de IoT de tipo SQL.

Utilize os seguintes recursos para saber como:

* Enviar telemetria a partir de dispositivos com o [introdução ao IoT Hub](quickstart-send-telemetry-java.md) tutorial.

* Controlar dispositivos interativamente (como ativar um fã de uma aplicação controlada pelo utilizador) com o [utilizar métodos diretos](quickstart-control-device-java.md) tutorial.