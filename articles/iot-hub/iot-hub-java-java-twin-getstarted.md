---
title: Começa com os gémeos Azure IoT Hub (Java) Microsoft Docs
description: Como usar gémeos de dispositivo Azure IoT Hub para adicionar tags e, em seguida, usar uma consulta IoT Hub. Utiliza o dispositivo Azure IoT SDK para a Java implementar a aplicação do dispositivo e o serviço Azure IoT SDK para a Java implementar uma aplicação de serviço que adiciona as etiquetas e executa a consulta IoT Hub.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: mqtt, devx-track-java
ms.openlocfilehash: b433ab29ab5a2520f503e002e630068126ddb328
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91838692"
---
# <a name="get-started-with-device-twins-java"></a>Começar com os gémeos do dispositivo (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Neste tutorial, cria-se duas aplicações para consolas Java:

* **add-tags-consulta**, uma aplicação java back-end que adiciona tags e consultas dispositivo gémeos.
* **dispositivo simulado**, uma aplicação de dispositivo Java que se conecta ao seu hub IoT e reporta a sua condição de conectividade usando uma propriedade relatada.

> [!NOTE]
> O artigo [Azure IoT SDKs](iot-hub-devguide-sdks.md) fornece informações sobre os SDKs Azure IoT que você pode usar para construir tanto dispositivos como aplicações de back-end.

## <a name="prerequisites"></a>Pré-requisitos

* [Kit de Desenvolvimento Java SE 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). Certifique-se de que seleciona **Java 8** sob **suporte a longo prazo** para obter downloads para JDK 8.

* [Maven 3](https://maven.apache.org/download.cgi)

* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de conexão do hub IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Criar a app de serviço

Nesta secção, cria-se uma aplicação Java que adiciona metadados de localização como uma etiqueta ao dispositivo twin no IoT Hub associado ao **myDeviceId**. A aplicação primeiro consulta o hub IoT para dispositivos localizados nos EUA e, em seguida, para dispositivos que reportam uma ligação de rede celular.

1. Na sua máquina de desenvolvimento, crie uma pasta vazia chamada **iot-java-twin-getstared**.

2. Na pasta **iot-java-twin-getstarted,** crie um projeto Maven denominado **add-tags-consulta** utilizando o seguinte comando no seu comando:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. Na sua instrução, navegue na pasta **de consulta de adicionar tags.**

4. Utilizando um editor de texto, abra o ficheiro **pom.xml** na pasta **de consulta de add-tags** e adicione a seguinte dependência ao nó **de dependências.** Esta dependência permite-lhe utilizar o pacote **iot-service-cliente** na sua app para comunicar com o seu hub IoT:

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

5. Adicione o nó **de construção** seguinte após o nó **de dependências.** Esta configuração instrui Maven a usar Java 1.8 para construir a app.

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

7. Utilizando um editor de texto, abra o **ficheiro add-tags\src\main\java\com\mycompany\app\App.java.**

8. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

9. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. Substitua-a pela cadeia de `{youriothubconnectionstring}` ligação do hub IoT copiada na [cadeia de ligação do hub IoT](#get-the-iot-hub-connection-string).

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

10. Atualizar a assinatura do método **principal** para incluir a seguinte `throws` cláusula:

    ```java
    public static void main( String[] args ) throws IOException
    ```

11. Substitua o código no método **principal** pelo seguinte código para criar os objetos **DeviceTwin** e **DeviceTwinDevice.** O objeto **DeviceTwin** lida com a comunicação com o seu hub IoT. O objeto **DeviceTwinDevice** representa o dispositivo twin com as suas propriedades e etiquetas:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

12. Adicione o seguinte `try/catch` bloco ao método **principal:**

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

13. Para atualizar a **região** e o dispositivo **vegetal** etiquetas duplas no seu dispositivo twin, adicione o seguinte código no `try` bloco:

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

14. Para consultar os gémeos do dispositivo no hub IoT, adicione o seguinte código ao `try` bloco depois do código que adicionou no passo anterior. O código faz duas consultas. Cada consulta devolve um máximo de 100 dispositivos.

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

15. Guarde e feche o **ficheiro add-tags-consulta\src\main\java\com\mycompany\app\App.java**

16. Construa a aplicação **de consulta de add-tags** e corrija quaisquer erros. No seu comando, navegue na pasta **de consulta de adição** e execute o seguinte comando:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Criar uma aplicação de dispositivo

Nesta secção, você cria uma aplicação de consola Java que define um valor de propriedade reportado que é enviado para IoT Hub.

1. Na pasta **iot-java-twin-getstarted,** crie um projeto Maven chamado **dispositivo simulado** utilizando o seguinte comando no seu comando:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. No seu comando, navegue para a pasta **do dispositivo simulado.**

3. Utilizando um editor de texto, abra o ficheiro **pom.xml** na pasta do **dispositivo simulado** e adicione as seguintes dependências ao nó de **dependências.** Esta dependência permite-lhe utilizar o pacote **iot-dispositivo-cliente** na sua aplicação para comunicar com o seu hub IoT.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > Pode verificar a versão mais recente do **iot-device-client** utilizando a [pesquisa Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Adicione a seguinte dependência ao nó **de dependências.** Esta dependência configura um NOP para a fachada de registo Apache [SLF4J,](https://www.slf4j.org/) que é usada pelo cliente do dispositivo SDK para implementar a exploração madeireira. Esta configuração é opcional, mas, se a omitir, poderá ver um aviso na consola quando executar a aplicação. Para obter mais informações sobre o registo no cliente do dispositivo SDK, consulte [registar-se](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging) nas *amostras do ficheiro SDK do dispositivo Azure IoT para* o ficheiro de leitura Java.

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

7. Utilizando um editor de texto, abra o **ficheiro simulado\src\main\java\com\mycompany\app\App.java.**

8. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

9. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. `{yourdeviceconnectionstring}`Substitua-a pela cadeia de ligação do dispositivo que copiou no [Registo de um novo dispositivo no hub IoT](#register-a-new-device-in-the-iot-hub).

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    Esta aplicação de exemplo utiliza a variável de **protocolo** para instanciar um objeto **DeviceClient**.

10. Adicione o seguinte método à classe **App** para imprimir informações sobre atualizações duplas:

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
          System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
      }
    ```

11. Substitua o código no método **principal** pelo seguinte código para:

    * Crie um cliente de dispositivo para comunicar com o IoT Hub.

    * Crie um objeto **do Dispositivo** para armazenar as propriedades gémeas do dispositivo.

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

12. Adicione o seguinte código ao método **principal** para criar uma propriedade reportada pelo **Tipo de conectividade** e enviá-la para o IoT Hub:

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

13. Adicione o seguinte código ao fim do método **principal.** Esperar pela tecla **Enter** permite tempo para o IoT Hub reportar o estado das operações gémeas do dispositivo.

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

14. Modifique a assinatura do método **principal** para incluir as exceções da seguinte forma:

     ```java
     public static void main(String[] args) throws URISyntaxException, IOException
     ```

15. Guarde e feche o **ficheiro simulado\src\main\java\com\mycompany\app\App.java.**

16. Construa a aplicação **de dispositivo simulado** e corrija quaisquer erros. No seu comando, navegue para a pasta do **dispositivo simulado** e execute o seguinte comando:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Executar as aplicações

Está agora pronto para executar as aplicações da consola.

1. Numa solicitação de comando na pasta **de consulta de add-tags,** executar o seguinte comando para executar a aplicação de serviço **de consulta de add-tags:**

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Screenshot que mostra a saída do comando para executar a aplicação de serviço de consulta de etiquetas adicionais.](./media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    Pode ver as etiquetas **da planta** e **da região** adicionadas ao dispositivo twin. A primeira consulta devolve o seu dispositivo, mas a segunda não.

2. Numa localização de comando na pasta do **dispositivo simulado,** executar o seguinte comando para adicionar a **conectividade A** propriedade reportada ao dispositivo twin:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![O cliente do dispositivo adiciona a propriedade reportada do Tipo de Conectividade](./media/iot-hub-java-java-twin-getstarted/device-app-1.png)

3. Numa solicitação de comando na pasta **de consulta de add-tags,** executar o seguinte comando para executar a aplicação de serviço **de consulta de add-tags** uma segunda vez:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![App de serviço Java IoT Hub para atualizar valores de marcação e executar consultas de dispositivos](./media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Agora que o seu dispositivo enviou a propriedade **de conectividadeType** para o IoT Hub, a segunda consulta devolve o seu dispositivo.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Adicionou metadados de dispositivo como tags de uma aplicação de back-end, e escreveu uma aplicação para reportar informações de conectividade do dispositivo no twin do dispositivo. Também aprendeu a consultar as informações gémeas do dispositivo utilizando a linguagem de consulta IoT Hub semelhante ao SQL.

Utilize os seguintes recursos para aprender a:

* Envie telemetria a partir de dispositivos com o Tutorial Get start com o tutorial [IoT Hub.](quickstart-send-telemetry-java.md)

* Dispositivos de controlo interativamente (como ligar um ventilador a partir de uma aplicação controlada pelo utilizador) com o tutorial [de métodos diretos Use.](quickstart-control-device-java.md)
