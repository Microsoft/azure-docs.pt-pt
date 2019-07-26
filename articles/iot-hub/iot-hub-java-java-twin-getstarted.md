---
title: Introdução ao dispositivo gêmeos (Java) do Hub IoT do Azure | Microsoft Docs
description: Como usar o dispositivo gêmeos do Hub IoT do Azure para adicionar marcas e, em seguida, usar uma consulta do Hub IoT. Use o SDK do dispositivo IoT do Azure para Java para implementar o aplicativo do dispositivo e o SDK do serviço IoT do Azure para Java para implementar um aplicativo de serviço que adiciona as marcas e executa a consulta do Hub IoT.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 07/04/2017
ms.openlocfilehash: 6d2d0540786f1aa4bec35cf4bec26212cb7df7ae
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404206"
---
# <a name="get-started-with-device-twins-java"></a>Introdução ao dispositivo gêmeos (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Neste tutorial, você criará dois aplicativos de console do Java:

* **Add-Tags – Query**, um aplicativo de back-end Java que adiciona marcas e consultas dispositivo gêmeos.
* **Simulated-Device**, um aplicativo de dispositivo Java que se conecta ao seu hub IOT e relata sua condição de conectividade usando uma propriedade relatada.

> [!NOTE]
> O artigo [SDKs do IOT do Azure](iot-hub-devguide-sdks.md) fornece informações sobre os SDKs do IOT do Azure que você pode usar para compilar dispositivos e aplicativos de back-end.

Para concluir este tutorial, precisa de:

* A versão mais recente de [Java SE Development Kit 8](https://aka.ms/azure-jdks)

* [Maven 3](https://maven.apache.org/install.html)

* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no Hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Criar o aplicativo de serviço

Nesta seção, você criará um aplicativo Java que adiciona metadados de local como uma marca para o dispositivo "r" no Hub IOT associado a DeviceID. O aplicativo primeiro consulta o Hub IoT para dispositivos localizados nos EUA e, em seguida, para dispositivos que relatam uma conexão de rede de celular.

1. No computador de desenvolvimento, crie uma pasta vazia chamada `iot-java-twin-getstarted`.

2. Na pasta, crie um projeto Maven chamado **Add-Tags-Query** usando o comando a seguir no prompt de comando. `iot-java-twin-getstarted` Tenha em atenção que se trata de um comando único, por extenso:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. No prompt de comando, navegue até a `add-tags-query` pasta.

4. Usando um editor de texto, abra `pom.xml` o arquivo `add-tags-query` na pasta e adicione a dependência a seguir ao nó **Dependencies** . Essa dependência permite que você use o pacote **IOT-Service-Client** em seu aplicativo para se comunicar com o Hub IOT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
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

6. Salve e feche o `pom.xml` arquivo.

7. Usando um editor de texto, abra `add-tags-query\src\main\java\com\mycompany\app\App.java` o arquivo.

8. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

9. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. Substitua `{youriothubconnectionstring}` pela cadeia de conexão do Hub IOT que você copiou anteriormente em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string):

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

10. Atualize a assinatura do método **principal** para incluir a `throws` seguinte cláusula:

    ```java
    public static void main( String[] args ) throws IOException
    ```

11. Adicione o código a seguir ao método **Main** para criar os objetos **DeviceTwin** e **DeviceTwinDevice** . O objeto **DeviceTwin** lida com a comunicação com o Hub IOT. O objeto **DeviceTwinDevice** representa o dispositivo de entrelaçamento com suas propriedades e marcas:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

12. Adicione o seguinte `try/catch` bloco ao método **Main** :

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

13. Para atualizar as marcas de área do dispositivo de **região** e de **planta** no seu dispositivo, adicione o seguinte `try` código ao bloco:

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

14. Para consultar o dispositivo gêmeos no Hub IOT, adicione o seguinte código ao `try` bloco após o código que você adicionou na etapa anterior. O código executa duas consultas. Cada consulta retorna um máximo de 100 dispositivos:

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

15. Salvar e fechar o `add-tags-query\src\main\java\com\mycompany\app\App.java` arquivo

16. Crie o aplicativo **Add-Tags-Query** e corrija os erros. No prompt de comando, navegue até a `add-tags-query` pasta e execute o seguinte comando:

    ```
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Criar uma aplicação de dispositivo

Nesta seção, você cria um aplicativo de console do Java que define um valor de propriedade relatado que é enviado ao Hub IoT.

1. Na pasta, crie um projeto Maven chamado **Simulated-Device** usando o comando a seguir no prompt de comando. `iot-java-twin-getstarted` Tenha em atenção que se trata de um comando único, por extenso:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. No prompt de comando, navegue até a `simulated-device` pasta.

3. Usando um editor de texto, abra `pom.xml` o arquivo `simulated-device` na pasta e adicione as seguintes dependências ao nó de **dependências** . Essa dependência permite que você use o pacote **IOT-Device-Client** em seu aplicativo para se comunicar com o Hub IOT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.14.2</version>
    </dependency>
    ```

    > [!NOTE]
    > Você pode verificar a versão mais recente do **IOT-Device-Client** usando a [pesquisa do Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Adicione o seguinte nó de **compilação** após o nó de **dependências** . Essa configuração instrui o Maven a usar o Java 1,8 para compilar o aplicativo:

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

5. Salve e feche o `pom.xml` arquivo.

6. Usando um editor de texto, abra `simulated-device\src\main\java\com\mycompany\app\App.java` o arquivo.

7. Adicione as seguintes declarações de **importação** ao ficheiro:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

8. Adicione as seguintes variáveis de nível de classe à classe **Aplicação**. Substituindo `{youriothubname}` pelo nome do Hub IOT e `{yourdevicekey}` pelo valor da chave do dispositivo gerado na seção *criar uma identidade do dispositivo* :

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    Esta aplicação de exemplo utiliza a variável de **protocolo** para instanciar um objeto **DeviceClient**. 

1. Adicione o seguinte método à classe **app** para imprimir informações sobre atualizações de dados de atualização:

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
          System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
      }
    ```

9. Adicione o seguinte código ao método **principal** para:
    * Crie um cliente de dispositivo para se comunicar com o Hub IoT.
    * Crie um objeto de **dispositivo** para armazenar as propriedades de FileUp do dispositivo.

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

10. Adicione o seguinte código ao método **Main** para criar uma propriedade  relatada de connectivitytype e enviá-la ao Hub IOT:

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

11. Adicione o código a seguir ao final do método **Main** . Aguardar a tecla **Enter** permite que o Hub IOT relate o status das operações de dispositivo de entrelaçamento:

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

1. Salve e feche o `simulated-device\src\main\java\com\mycompany\app\App.java` arquivo.

13. Crie o aplicativo **Simulated-Device** e corrija os erros. No prompt de comando, navegue até a `simulated-device` pasta e execute o seguinte comando:

    ```
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Executar as aplicações

Agora você está pronto para executar os aplicativos de console.

1. Em um prompt de comando na `add-tags-query` pasta, execute o seguinte comando para executar o aplicativo de serviço **Add-Tags-Query** :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplicativo de serviço do Hub IoT do Java para atualizar valores de marca e executar consultas de dispositivo](./media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    Você pode ver as marcas de **fábrica** e de **região** adicionadas ao dispositivo. A primeira consulta retorna seu dispositivo, mas a segunda não.

2. Em um prompt de comando na `simulated-device` pasta, execute o seguinte comando para adicionar a propriedade relatada **connectivitytype** ao dispositivo.

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![O cliente do dispositivo adiciona a propriedade relatada * * connectivitytype * *](./media/iot-hub-java-java-twin-getstarted/device-app-1.png)

3. Em um prompt de comando na `add-tags-query` pasta, execute o seguinte comando para executar o aplicativo de serviço **Add-Tags-consulta** uma segunda vez:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplicativo de serviço do Hub IoT do Java para atualizar valores de marca e executar consultas de dispositivo](./media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Agora seu dispositivo enviou a propriedade **connectivitytype** ao Hub IOT, a segunda consulta retorna seu dispositivo.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, configurou um novo Hub IoT no portal do Azure e, em seguida, criou uma identidade de dispositivo no registo de identidades do Hub IoT. Você adicionou metadados de dispositivo como marcas de um aplicativo de back-end e escreveu um aplicativo de dispositivo para relatar informações de conectividade do dispositivo no dispositivo. Você também aprendeu como consultar as informações de dados do dispositivo usando a linguagem de consulta do Hub IoT do tipo SQL.

Use os seguintes recursos para aprender a:

* Envie telemetria de dispositivos com o tutorial introdução [ao Hub IOT](quickstart-send-telemetry-java.md) .

* Controlar dispositivos interativamente (como ativar um ventilador de um aplicativo controlado pelo usuário) com o tutorial [usar métodos diretos](quickstart-control-device-java.md) .