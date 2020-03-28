---
title: 'Tutorial: Monitor a ioT espaço de dispositivos - Azure Digital Twins Microsoft Docs'
description: Aprenda a fornecer os seus recursos espaciais e a monitorizar as condições de trabalho com as Gémeas Digitais Azure utilizando os passos deste tutorial.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 01/10/2020
ms.openlocfilehash: 6cf6a8f7de181a81d60028e33ba2631815c8ca04
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75895372"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins-preview"></a>Tutorial: Providenciar as suas condições de trabalho de construção e monitorização com pré-visualização de gémeos digitais Azure

Este tutorial demonstra como usar a Pré-visualização de Gémeos Digitais Azure para monitorizar os seus espaços para condições de temperatura e nível de conforto desejados. Depois de [configurar a sua construção](tutorial-facilities-setup.md)de amostras, pode fornecer o seu edifício e executar funções personalizadas nos dados do seu sensor utilizando os passos deste tutorial.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Defina condições para monitorizar.
> * Criar uma função definida pelo utilizador (UDF).
> * Simular dados de sensores.
> * Obtenha resultados de uma função definida pelo utilizador.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial assume que [terminou a sua configuração de Gémeos Digitais Azure.](tutorial-facilities-setup.md) Antes de avançar, confirme que tem:

- Uma [conta Azure.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Uma instância do Digital Twins em execução. 
- As [amostras Digital Twins C#](https://github.com/Azure-Samples/digital-twins-samples-csharp) foram descarregadas e extraídas na sua máquina de trabalho. 
- [.NET Core SDK versão 2.1.403 ou mais tarde](https://www.microsoft.com/net/download) na sua máquina de desenvolvimento para construir e executar a amostra. Corra `dotnet --version` para verificar se a versão certa está instalada. 
- [Visual Studio Code](https://code.visualstudio.com/), para explorar o código de exemplo. 

>[!TIP]
> Use um nome único de gémeos digitais se estiver a fornecer uma nova instância.

## <a name="define-conditions-to-monitor"></a>Definir as condições a monitorizar

Pode definir um conjunto de condições específicas para monitorizar no dispositivo ou dados do sensor, *chamados matchers*. Pode então definir funções chamadas *funções definidas pelo utilizador*. As funções definidas pelo utilizador executam a lógica personalizada dos dados provenientes dos seus espaços e dispositivos, quando ocorrem as condições especificadas pelos matchers. Para mais informações, leia o [processamento de dados e as funções definidas pelo utilizador.](concepts-user-defined-functions.md) 

No exemplo de projeto **occupancy-quickstart**, abra o ficheiro **src\actions\provisionSample.yaml** no Visual Studio Code. Repare na secção que começa com o tipo **matchers**. Cada entrada deste tipo cria um matcher com o **nome**especificado . O matcher monitorizará um sensor de **dados tipoTypeValue**. Note como se relaciona com o espaço chamado *Focus Room A1,* que tem um nó de **dispositivos** que contém alguns sensores. Para fornecer um matcher que rastreie um destes sensores, certifique-se de que os seus **dadosTypeValue** correspondem ao **dataType**do sensor . 

Adicione o seguinte matcher abaixo dos matchers existentes. Certifique-se de que as teclas estão alinhadas e que os espaços não são substituídos por separadores. Estas linhas também estão presentes no ficheiro *provisionSample.yaml* como linhas comentadas. Pode descocomentá-los removendo o `#` personagem em frente a cada linha.

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

Este matcher irá `SAMPLE_SENSOR_TEMPERATURE` rastrear o sensor que adicionou [no primeiro tutorial.](tutorial-facilities-setup.md) 

## <a name="create-a-user-defined-function"></a>Criar uma função definida Pelo utilizador

Pode utilizar funções definidas pelo utilizador para personalizar o processamento dos dados do seu sensor. São códigoS JavaScript personalizados que podem ser executados dentro da sua instância De Gémeos Digitais Azure, quando ocorrem condições específicas descritas pelos matchers. Pode criar matchers e funções definidas pelo utilizador par cada sensor que queira monitorizar. Para mais informações, leia o [processamento de dados e as funções definidas pelo utilizador.](concepts-user-defined-functions.md) 

No ficheiro *sampleprovisionSample.yaml,* procure uma secção que comece com as **funções definidas**pelo tipo de utilizador . Esta secção prevê uma função definida pelo utilizador com um **nome**dado . Esta UDF atua na lista de matchers sob **matcherNames**. Repare que pode fornecer o seu próprio ficheiro JavaScript para a UDF como o **script**.

Veja também a secção com o nome **roleassignments**. Atribui a função de Administrador Espacial à função definida pelo utilizador. Esta função permite-lhe aceder aos eventos que provêm de qualquer um dos espaços aprovisionados. 

1. Configure a UDF para incluir o matcher “temperatura” ao adicionar ou remover o comentário na linha seguinte do nó `matcherNames` do ficheiro *provisionSample.yaml*:

    ```yaml
            - Matcher Temperature
    ```

1. Abra o ficheiro **src\actions\userDefinedFunctions\availability.js** no editor. Este é o ficheiro referenciado no elemento **script** de *provisionSample.yaml*. A função definida pelo utilizador neste ficheiro procura condições quando não é detetado nenhum movimento na sala e os níveis de dióxido de carbono estão abaixo de 1.000 ppm. 

   Modifique o ficheiro JavaScript para monitorizar a temperatura e outras condições. Adicione as seguintes linhas de código para procurar condições quando não for detetado movimento na sala, os níveis de dióxido de carbono estão abaixo de 1.000 ppm, e a temperatura é inferior a 78 graus Fahrenheit.

   >[!NOTE]
   > Esta secção modifica o ficheiro *src\actions\userDefinedFunctions\availability.js* para que possa aprender em detalhe uma forma de escrever uma função definida pelo utilizador. No entanto, pode optar por utilizar diretamente o ficheiro [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) na sua configuração. que tem todas as alterações necessárias para este tutorial. Se utilizar este ficheiro, certifique-se de que utiliza o nome de ficheiro correto para a chave de **script** em [src\actions\provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

    a. Na parte superior do ficheiro, adicione as linhas seguintes para temperatura, abaixo do comentário `// Add your sensor type here`:

    ```JavaScript
        var temperatureType = "Temperature";
        var temperatureThreshold = 78;
    ```

    b. Adicione as seguintes `var motionSensor`linhas após a declaração `// Add your sensor variable here`que define, abaixo do comentário:

     ```JavaScript
        var temperatureSensor = otherSensors.find(function(element) {
            return element.DataType === temperatureType;
        });
    ```

    c. Adicione a seguinte linha após `var carbonDioxideValue`a declaração `// Add your sensor latest value here`que define, abaixo do comentário:

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
        }
        else {
            log(`${noAvailableOrFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAvailableOrFresh);

            // Set up custom notification for poor air quality
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

1. Abra uma janela de comando e vá para a pasta **de ocupação-quickstart\src**. Execute o seguinte comando para fornecer o seu gráfico de inteligência espacial e função definida pelo utilizador:

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   >[!IMPORTANT]
   > Para evitar o acesso não autorizado à sua API de Gestão de Gémeos Digitais, a aplicação de **início rápido de ocupação** requer que você assine com as credenciais da sua conta Azure. Guarda as suas credenciais por um breve período, para que não precise de assinar cada vez que as executa. A primeira vez que este programa corre, e quando as suas credenciais guardadas expiram depois disso, a aplicação direciona-o para uma página de entrada e dá um código específico para a sessão para introduzir nessa página. Siga os pedidos para iniciar sessão com a conta do Azure.

1. Depois de autenticada a sua conta, a aplicação começa a criar um gráfico espacial de amostra, tal como configurado na *provisãoSample.yaml*. Espere até que o fornecimento termine. Vai levar alguns minutos. Depois disso, observe as mensagens na janela de comando e note como o seu gráfico espacial é criado. Note como a aplicação cria um hub IoT no nó raiz ou no `Venue`.

1. Desde a saída na janela de `ConnectionString`comando, `Devices` copie o valor de, por baixo da secção, para a sua prancheta. Você precisará deste valor para simular a ligação do dispositivo na próxima secção.

    [![Amostra de provisão](./media/tutorial-facilities-udf/run-provision-sample.png)](./media/tutorial-facilities-udf/run-provision-sample.png#lightbox)

>[!TIP]
> Se receber uma mensagem de erro semelhante à "Operação I/O foi abortada devido a uma saída de fio ou a um pedido de pedido de aplicação" no meio do fornecimento, tente executar novamente o comando. Isto pode acontecer se o cliente http se desisse de um problema de rede.

## <a name="simulate-sensor-data"></a>Simular dados do sensor

Nesta secção, utilizará o projeto chamado *conectividade dispositivo-conectividade* na amostra. Simulará dados de sensores para detetar movimento, temperatura e dióxido de carbono. O projeto gera valores aleatórios para os sensores e envia-os para o hub IoT mediante a utilização da cadeia de ligação do dispositivo.

1. Numa janela de comando separada, vá à amostra De Gémeos Digitais Azure e, em seguida, à pasta **de conectividade do dispositivo.**

1. Execute este comando para confirmar que as dependências do projeto estão corretas:

    ```cmd/sh
    dotnet restore
    ```

1. Abra o ficheiro [appsettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) no seu editor e edite os seguintes valores:

   a. **DeviceConnectionString**: atribua o valor de `ConnectionString` na janela de saída da secção anterior. Copie completamente esta cadeia, dentro das cotações, para que o simulador possa ligar-se corretamente ao centro IoT.

   b. **HardwareId** dentro da matriz **de Sensores:** Como está a simular eventos a partir de sensores fornecidos à sua instância `sensors` De Gémeos Digitais Azure, o ID de hardware e os nomes dos sensores neste ficheiro devem corresponder ao nó do ficheiro *provisionSample.yaml.*

      Adicione uma nova entrada para o sensor de temperatura. O nó **sensorem** as *definições.json* deve parecer o seguinte:

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

   >[!NOTE]
   > Uma vez que a amostra de simulação não comunica diretamente com a sua instância De Gémeos Digitais, não requer que se autentique.

## <a name="get-results-of-the-user-defined-function"></a>Obtenha resultados da função definida pelo utilizador

A função definida pelo utilizador é executada sempre que a sua instância recebe dados de dispositivos e de sensores. Esta secção consulta a sua instância Azure Digital Twins para obter os resultados da função definida pelo utilizador. Será notificado em tempo real, quando um quarto estiver disponível, que o ar é fresco e a temperatura está certa. 

1. Abra a janela de comando que usou para fornecer a amostra, ou uma nova janela de comando, e vá para a pasta de **ocupação-quickstart\src** da amostra novamente.

1. Execute o comando seguinte e inicie sessão quando lhe for pedido:

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

A janela de saída mostra como a função definida pelo utilizador funciona e interceta eventos da simulação do dispositivo. 

   [![Saída para a UDF](./media/tutorial-facilities-udf/adt-tutorial-udf-running.png)](./media/tutorial-facilities-udf/adt-tutorial-udf-running.png#lightbox)

Se a condição monitorizada for satisfeita, a função definida pelo utilizador define o valor do espaço com a mensagem relevante, como vimos [anteriormente](#create-a-user-defined-function). A `GetAvailableAndFreshSpaces` função imprime a mensagem na consola.

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser parar de explorar as Gémeas Digitais Azure neste momento, sinta-se à vontade para apagar os recursos criados neste tutorial:

1. A partir do menu esquerdo no [portal Azure](https://portal.azure.com), selecione **Todos os recursos,** selecione o seu grupo de recursos Digital Twins e selecione **Delete**.

    >[!TIP]
    > Se teve problemas em apagar a sua instância De Gémeos Digitais, foi lançada uma atualização de serviço com a correção. Por favor, tente apagar o seu caso.

2. Se necessário, elimine as aplicações da amostra na sua máquina de trabalho.

## <a name="next-steps"></a>Passos seguintes

Agora que disponibilizou os seus espaços e criou um quadro para desencadear notificações personalizadas, pode ir a qualquer um dos seguintes tutoriais:

> [!div class="nextstepaction"]
> [Tutorial: Receive notifications from your Azure Digital Twins spaces using Logic Apps](tutorial-facilities-events.md) (Tutorial: Utilizar o Logic Apps para receber notificações dos seus espaços do Azure Digital Twins)

> [!div class="nextstepaction"]
> [Tutorial: Visualize and analyze events from your Azure Digital Twins spaces using Time Series Insights](tutorial-facilities-analyze.md) (Tutorial: Visualizar e analisar eventos dos seus espaços do Azure Digital Twins com o Time Series Insights)
