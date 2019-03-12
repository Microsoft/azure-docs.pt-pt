---
title: 'Tutorial: Monitorizar um espaço com o Azure Digital Twins | Microsoft Docs'
description: Saiba como aprovisionar os recursos espaciais e monitorizar as condições de trabalho duplos Digital do Azure, utilizando os passos neste tutorial.
services: digital-twins
author: dsk-2015
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: dkshir
ms.openlocfilehash: ad6c2625dc56dc3a3155183a04b712122a3b10f1
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57535387"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins"></a>Tutorial: Aprovisionar a sua criação e o monitor trabalhar condições duplos Digital do Azure

Este tutorial demonstra como utilizar o Azure Digital Twins para monitorizar os seus espaços relativamente às condições de temperatura e ao nível de conforto pretendidos. Depois de [configurar a criação de exemplo](tutorial-facilities-setup.md), pode aprovisionar a criar e executar funções personalizadas nos seus dados de sensor, utilizando os passos neste tutorial.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Defina condições para monitorizar.
> * Crie uma função definida pelo utilizador (UDF).
> * Simule dados de sensor.
> * Obter resultados de uma função definida pelo utilizador.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que tenha [concluída a configuração de duplos Digital do Azure](tutorial-facilities-setup.md). Antes de avançar, confirme que tem:

- Uma [conta do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uma instância do Digital Twins em execução. 
- Os [exemplos do Digital Twins em C#](https://github.com/Azure-Samples/digital-twins-samples-csharp) transferidos e extraídos para o computador de trabalho. 
- [.NET core SDK versão 2.1.403 ou posterior](https://www.microsoft.com/net/download) no computador de desenvolvimento para compilar e executar o exemplo. Executar `dotnet --version` para verificar se a versão correta está instalada. 
- [Visual Studio Code](https://code.visualstudio.com/), para explorar o código de exemplo. 

## <a name="define-conditions-to-monitor"></a>Definir as condições a monitorizar

Pode definir um conjunto de condições específicas para monitorizar os dados de sensor ou dispositivo, chamado *matchers*. Em seguida, pode definir funções chamadas *funções definidas pelo utilizador*. Funções definidas pelo utilizador executar a lógica personalizada em dados provenientes da sua espaços e os dispositivos, quando as condições especificadas pelos matchers ocorrem. Para obter mais informações, leia [processamento de dados e funções definidas pelo utilizador](concepts-user-defined-functions.md). 

Partir do **ocupação-quickstart** projeto de exemplo, abra o arquivo **src\actions\provisionSample.yaml** no Visual Studio Code. Repare na secção que começa com o tipo **matchers**. Cada entrada sob este tipo cria na ferramenta de correspondência com a especificado **nome**. Na ferramenta de correspondência que irá monitorizar um sensor do tipo **dataTypeValue**. Observe como ele se relaciona com o espaço com o nome *A1 de sala de foco*, que tem um **dispositivos** nó que contém alguns sensores. Para aprovisionar na ferramenta de correspondência que irá controlar a um destes sensores, certifique-se de que seus **dataTypeValue** corresponda o sensor **dataType**. 

Adicione a seguinte na ferramenta de correspondência, abaixo dos matchers existentes. Certificar-se de que as chaves estão bem alinhadas e espaços não são substituídos por tabulações.

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

Na ferramenta de correspondência controlará o sensor SAMPLE_SENSOR_TEMPERATURE que adicionou no [o primeiro tutorial](tutorial-facilities-setup.md). Estas linhas também estão presentes no *provisionSample.yaml* ficheiro como linhas Comentada. Pode remover os comentários-los ao remover o `#` caractere à frente de cada linha.

<a id="udf"></a>

## <a name="create-a-user-defined-function"></a>Criar uma função definida Pelo utilizador

Pode utilizar funções definidas pelo utilizador para personalizar o processamento dos seus dados de sensor. Eles são o código JavaScript personalizado que pode ser executado dentro de sua instância de duplos Digital do Azure, quando condições específicas, conforme descrito pelos matchers ocorrem. Pode criar matchers e funções definidas pelo utilizador para cada sensor que pretende monitorizar. Para obter mais informações, leia [processamento de dados e funções definidas pelo utilizador](concepts-user-defined-functions.md). 

No ficheiro de provisionSample.yaml de exemplo, procure uma seção que começa com o tipo **userdefinedfunctions**. Esta secção fornece uma função definida pelo utilizador com um determinado **nome**. Este UDF atua na lista de matchers sob **matcherNames**. Repare que pode fornecer o seu próprio ficheiro JavaScript para a UDF como o **script**.

Veja também a secção com o nome **roleassignments**. Atribui a função de administrador de espaço para a função definida pelo utilizador. Esta função permite que os eventos provenientes de nenhum dos espaços de aprovisionamento de acesso. 

1. Configurar a UDF para incluir na ferramenta de correspondência de temperatura adicionando ou interessantes a seguinte linha no `matcherNames` nó do arquivo provisionSample.yaml:

    ```yaml
            - Matcher Temperature
    ```

1. Abra o ficheiro **src\actions\userDefinedFunctions\availability.js** no seu editor. Este é o arquivo referenciado no **script** elemento de provisionSample.yaml. A função definida pelo utilizador neste ficheiro procura condições quando não é detetado o nenhum movimento na sala e níveis de dióxido de carbono estão abaixo ppm 1000. 

   Modifique o arquivo JavaScript a temperatura do monitor e outras condições. Adicione as seguintes linhas de código para procurar por condições quando não é detetado o nenhum movimento na sala, níveis de dióxido de carbono estão abaixo ppm 1000 e temperatura for inferior 78 graus Fahrenheit.

   > [!NOTE]
   > Esta secção modifica o arquivo *src\actions\userDefinedFunctions\availability.js* para que possa saber em detalhe uma maneira de escrever uma função definida pelo utilizador. No entanto, pode optar por utilizar diretamente o ficheiro [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) em sua configuração. que tem todas as alterações necessárias para este tutorial. Se usar esse arquivo em vez disso, certifique-se utilizar o nome de ficheiro correto para o **script** chave [src\actions\provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

    a. Na parte superior do ficheiro, adicione as linhas seguintes para temperatura, abaixo do comentário `// Add your sensor type here`:

    ```JavaScript
        var temperatureType = "Temperature";
        var temperatureThreshold = 78;
    ```

    b. Adicione as seguintes linhas após a instrução que define `var motionSensor`, abaixo o comentário `// Add your sensor variable here`:

     ```JavaScript
        var temperatureSensor = otherSensors.find(function(element) {
            return element.DataType === temperatureType;
        });
    ```

    c. Adicione a seguinte linha após a instrução que define `var carbonDioxideValue`, abaixo o comentário `// Add your sensor latest value here`:

    ```JavaScript
        var temperatureValue = getFloatValue(temperatureSensor.Value().Value);
    ```

    d. Remova as linhas de código seguintes, abaixo do comentário `// Modify this line to monitor your sensor value`:

     ```JavaScript
        if(carbonDioxideValue === null || motionValue === null) {
            sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide or motion are null, returning");
            return;
        }
    ```

    Substitua-as pelas linhas seguintes:

    ```JavaScript
        if(carbonDioxideValue === null || motionValue === null || temperatureValue === null){
            sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide, motion, or temperature are null, returning");
            return;
        }
    ```

    e. Remova as linhas de código seguintes, abaixo do comentário `// Modify these lines as per your sensor`:

    ```JavaScript
        var availableFresh = "Room is available and air is fresh";
        var noAvailableOrFresh = "Room is not available or air quality is poor";
    ```

    Substitua-as pelas linhas seguintes:

    ```JavaScript
        var alert = "Room with fresh air and comfortable temperature is available.";
        var noAlert = "Either room is occupied, or working conditions are not right.";
    ```

    f. Remova o bloco de código *if-else* seguinte, a seguir ao comentário `// Modify this code block for your sensor`:

    ```JavaScript
        // If carbonDioxide less than threshold and no presence in the room => log, notify and set parent space computed value
        if(carbonDioxideValue < carbonDioxideThreshold && !presence) {
            log(`${availableFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, availableFresh);

            // Set up custom notification for air quality
            parentSpace.Notify(JSON.stringify(availableFresh));
        }
        else {
            log(`${noAvailableOrFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAvailableOrFresh);

            // Set up custom notification for air quality
            parentSpace.Notify(JSON.stringify(noAvailableOrFresh));
        }
    ```

    Substitua-o pelo bloco *if-else* seguinte:

    ```JavaScript
        // If sensor values are within range and room is available
        if(carbonDioxideValue < carbonDioxideThreshold && temperatureValue < temperatureThreshold && !presence) {
            log(`${alert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

            // log, notify and set parent space computed value
            setSpaceValue(parentSpace.Id, spaceAvailFresh, alert);

            // Set up notification for this alert
            parentSpace.Notify(JSON.stringify(alert));
        }
        else {
            log(`${noAlert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

            // log, notify and set parent space computed value
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAlert);
        }
    ```

    A UDF modificada procurará uma condição em que uma sala fica disponível e cujos níveis de dióxido de carbono e a temperatura estejam dentro de um limite tolerável. Quando essa condição for satisfeita, gerará uma notificação com a declaração `parentSpace.Notify(JSON.stringify(alert));`. Definirá o valor do espaço monitorizado independentemente de a condição ser ou não satisfeita, com a mensagem correspondente.

    g. Guarde o ficheiro.

1. Abra uma janela de comandos e aceda à pasta **ocupação quickstart\src**. Execute o seguinte comando para aprovisionar o seu gráfico intelligence espaciais e a função definida pelo utilizador:

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   > [!IMPORTANT]
   > Para evitar acessos não autorizados à sua API de gestão de duplos Digital, o **ocupação-quickstart** aplicação requer que inicie sessão com as credenciais da conta do Azure. Guarda as suas credenciais para um breve período, portanto, não poderá ter de iniciar sessão sempre que executá-lo. Executar este programa de pela primeira vez, e quando expiram as suas credenciais guardadas depois disso, a aplicação direciona-o para uma página de início de sessão e fornece um código específico da sessão para introduzir nessa página. Siga os pedidos para iniciar sessão com a conta do Azure.

1. Depois de sua conta for autenticada, o aplicativo começa criando um gráfico de geográficos de exemplo, conforme configurado na provisionSample.yaml. Aguarde até a conclusão do aprovisionamento. Irá demorar alguns minutos. Depois disso, observe as mensagens na janela de comando e repare como o seu gráfico espacial é criado. Observe como a aplicação cria um hub IoT no nó raiz ou a `Venue`.

1. A partir da saída na janela de comando, copie o valor da `ConnectionString`, sob o `Devices` seção, para a área de transferência. Precisará desse valor para simular a ligação do dispositivo na próxima seção.

    ![Exemplo de aprovisionamento](./media/tutorial-facilities-udf/run-provision-sample.png)

> [!TIP]
> Se obtiver uma mensagem de erro semelhante a "a operação de e/s foi abortada devido a um pedido de aplicação ou de uma saída de thread" no meio o aprovisionamento, tentar executar o comando novamente. Isto pode acontecer se o cliente HTTP excedeu o limite de tempo de um problema de rede.

<a id="simulate"></a>

## <a name="simulate-sensor-data"></a>Simular dados do sensor

Nesta secção, vai utilizar o projeto com o nome *conectividade do dispositivo* no exemplo. Irá simular dados de sensor para deteção de movimento, temperatura e emissões de dióxido de carbono. O projeto gera valores aleatórios para os sensores e envia-os para o hub IoT mediante a utilização da cadeia de ligação do dispositivo.

1. Na janela de comando separada, vá para o exemplo de duplos Digital do Azure e, em seguida, para o **conectividade do dispositivo** pasta.

1. Execute este comando para confirmar que as dependências do projeto estão corretas:

    ```cmd/sh
    dotnet restore
    ```

1. Abra o [appSettings](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) no seu editor de ficheiros e edite os valores seguintes:

   a. **DeviceConnectionString**: Atribuir o valor de `ConnectionString` na janela de saída da secção anterior. Copie essa cadeia de caracteres completamente, dentro das aspas, para que o simulador possam ligar-se corretamente com o hub IoT.

   b. **HardwareId** dentro do **sensores** matriz: Uma vez que está sendo simulada eventos a partir de sensores aprovisionados para a sua instância de duplos Digital do Azure, o ID de hardware e os nomes dos sensores neste ficheiro devem corresponder a `sensors` nó do arquivo provisionSample.yaml.

      Adicione uma nova entrada para o sensor de temperatura. O **sensores** nó no appSettings deve ter um aspeto semelhante ao seguinte:

      ```JSON
      "Sensors": [{
        "DataType": "Motion",
        "HardwareId": "SAMPLE_SENSOR_MOTION"
      },{
        "DataType": "CarbonDioxide",
        "HardwareId": "SAMPLE_SENSOR_CARBONDIOXIDE"
      },{
        "DataType": "Temperature",
        "HardwareId": "SAMPLE_SENSOR_TEMPERATURE"
      }]
      ```

1. Execute este comando para começar a simulação dos eventos do dispositivo para temperatura, movimento e dióxido de carbono:

    ```cmd/sh
    dotnet run
    ```

   > [!NOTE]
   > Uma vez que o exemplo de simulação não comunicam diretamente com a instância de duplos Digital, não necessita que a autenticação.

## <a name="get-results-of-the-user-defined-function"></a>Obter resultados da função definida pelo utilizador

A função definida pelo utilizador é executada sempre que a sua instância recebe dados de dispositivos e de sensores. Esta secção consulta sua instância de duplos Digital do Azure para obter os resultados da função definida pelo utilizador. Verá em tempo real, quando uma sala estiver disponível, que o ar está fresco e temperatura é adequada. 

1. Abra a janela de comando que utilizou para aprovisionar o exemplo ou uma nova janela de comando e vá para o **ocupação quickstart\src** pasta do exemplo novamente.

1. Execute o comando seguinte e inicie sessão quando lhe for pedido:

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

A janela de saída mostra como a função definida pelo utilizador é executado e intercepta eventos a partir da simulação de dispositivo. 

   ![Saída para o UDF](./media/tutorial-facilities-udf/udf-running.png)

Se a condição monitorizada for cumprida, a função definida pelo usuário define o valor do espaço com a mensagem relevante, como vimos [anteriores](#udf). O `GetAvailableAndFreshSpaces` função imprime a mensagem na consola.

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender parar a explorar duplos Digital do Azure neste momento, fique à vontade eliminar recursos criados neste tutorial:

1. No menu do lado esquerdo da [portal do Azure](https://portal.azure.com), selecione **todos os recursos**, selecione o grupo de recursos digitais duplos e selecione **eliminar**.

    > [!TIP]
    > Se teve problemas ao eliminar a instância de duplos Digital, uma atualização de serviço capacidade foi implementada com a correção. Volte a tentar eliminar a instância.

2. Se necessário, elimine os aplicativos de exemplo no seu computador de trabalho.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprovisionou seus espaços e Criei uma estrutura para acionar notificações personalizadas, pode aceder a qualquer um dos seguintes tutoriais:

> [!div class="nextstepaction"]
> [Tutorial: Receber notificações do seus espaços duplos Digital do Azure com o Logic Apps](tutorial-facilities-events.md)

> [!div class="nextstepaction"]
> [Tutorial: Visualize e analise os eventos a partir de seus espaços duplos Digital do Azure com o Time Series Insights](tutorial-facilities-analyze.md)
