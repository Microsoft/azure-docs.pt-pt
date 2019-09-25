---
title: 'Tutorial: Monitorizar um espaço com o Azure Digital Twins | Microsoft Docs'
description: Saiba como provisionar seus recursos espaciais e monitorar as condições de trabalho com o gêmeos digital do Azure usando as etapas deste tutorial.
services: digital-twins
author: alinamstanciu
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: alinast
ms.openlocfilehash: e483ac8e56ce39cbb05c5d00634c6327b497bab5
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219906"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins-preview"></a>Tutorial: Provisione sua criação e monitore condições de trabalho com o Azure digital gêmeos Preview

Este tutorial demonstra como usar o Azure digital gêmeos Preview para monitorar seus espaços para as condições de temperatura e o nível de conforto desejado. Depois de [Configurar o Build de exemplo](tutorial-facilities-setup.md), você pode provisionar seu edifício e executar funções personalizadas nos dados do sensor usando as etapas deste tutorial.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Defina as condições a serem monitoradas.
> * Crie uma UDF (função definida pelo usuário).
> * Simular dados de sensor.
> * Obter resultados de uma função definida pelo usuário.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que você [concluiu a instalação do gêmeos digital do Azure](tutorial-facilities-setup.md). Antes de avançar, confirme que tem:

- Uma [conta do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uma instância do Digital Twins em execução. 
- Os [exemplos do Digital Twins em C#](https://github.com/Azure-Samples/digital-twins-samples-csharp) transferidos e extraídos para o computador de trabalho. 
- [SDK do .NET Core versão 2.1.403 ou posterior](https://www.microsoft.com/net/download) em seu computador de desenvolvimento para compilar e executar o exemplo. Execute `dotnet --version` para verificar se a versão correta está instalada. 
- [Visual Studio Code](https://code.visualstudio.com/), para explorar o código de exemplo. 

> [!TIP]
> Use um nome de instância de gêmeos digital exclusivo se você estiver Provisionando uma nova instância.

## <a name="define-conditions-to-monitor"></a>Definir as condições a monitorizar

Você pode definir um conjunto de condições específicas para monitorar nos dados do dispositivo ou do sensor, chamados *correspondências*. Em seguida, você pode definir funções chamadas *de funções definidas pelo usuário*. As funções definidas pelo usuário executam lógica personalizada nos dados provenientes de seus espaços e dispositivos, quando as condições especificadas pelos correspondentes ocorrerem. Para obter mais informações, leia [processamento de dados e funções definidas pelo usuário](concepts-user-defined-functions.md). 

No projeto de exemplo **ocupação-início rápido** , abra o arquivo **src\actions\provisionSample.YAML** em Visual Studio Code. Repare na secção que começa com o tipo **matchers**. Cada entrada sob esse tipo cria uma correspondência com o **nome**especificado. O correspondente monitorará um sensor do tipo **Datatypevalue**. Observe como ele se relaciona ao espaço chamado sala de *foco a1*, que tem um nó de **dispositivos** que contém alguns sensores. Para provisionar um correspondente que rastreará um desses sensores, certifique-se de que seu **tipo** de dados corresponde ao **DataType**do sensor. 

Adicione o seguinte correspondente abaixo dos correspondentes existentes. Verifique se as chaves estão alinhadas e se os espaços não são substituídos por guias. Essas linhas também estão presentes no arquivo *provisionSample. YAML* como linhas comentadas. Você pode remover os comentários removendo o `#` caractere na frente de cada linha.

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

Esse correspondente acompanhará o `SAMPLE_SENSOR_TEMPERATURE` sensor que você adicionou no [primeiro tutorial](tutorial-facilities-setup.md). 

## <a name="create-a-user-defined-function"></a>Criar uma função definida Pelo utilizador

Você pode usar funções definidas pelo usuário para personalizar o processamento dos dados do sensor. São códigos JavaScript personalizados que podem ser executados em sua instância de gêmeos digital do Azure, quando condições específicas, conforme descrito pelos correspondentes, ocorrem. Você pode criar correspondências e funções definidas pelo usuário para cada sensor que você deseja monitorar. Para obter mais informações, leia [processamento de dados e funções definidas pelo usuário](concepts-user-defined-functions.md). 

No arquivo *provisionSample. YAML* de exemplo, procure uma seção que comece com o tipo **UserDefinedFunctions**. Esta seção provisiona uma função definida pelo usuário com um determinado **nome**. Esse UDF atua na lista de correspondências em **matcherNames**. Repare que pode fornecer o seu próprio ficheiro JavaScript para a UDF como o **script**.

Veja também a secção com o nome **roleassignments**. Ele atribui a função de administrador de espaço à função definida pelo usuário. Essa função permite que ele acesse os eventos provenientes de qualquer um dos espaços provisionados. 

1. Configure a UDF para incluir o matcher “temperatura” ao adicionar ou remover o comentário na linha seguinte do nó `matcherNames` do ficheiro *provisionSample.yaml*:

    ```yaml
            - Matcher Temperature
    ```

1. Abra o arquivo **src\actions\userDefinedFunctions\availability.js** no editor. Esse é o arquivo referenciado no elemento **script** de *provisionSample. YAML*. A função definida pelo usuário nesse arquivo procura condições quando nenhum movimento é detectado nos níveis de sala e dióxido de carbono estão abaixo de 1.000 ppm. 

   Modifique o arquivo JavaScript para monitorar a temperatura e outras condições. Adicione as seguintes linhas de código para procurar condições quando nenhum movimento for detectado na sala, os níveis de dióxido de carbono estão abaixo de 1.000 ppm e a temperatura está abaixo de 78 graus Fahrenheit.

   > [!NOTE]
   > Esta seção modifica o arquivo *src\actions\userDefinedFunctions\availability.js* para que você possa aprender em detalhes uma maneira de gravar uma função definida pelo usuário. No entanto, você pode optar por usar diretamente o arquivo [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) na sua configuração. que tem todas as alterações necessárias para este tutorial. Se você usar esse arquivo em vez disso, certifique-se de usar o nome de arquivo correto para a chave de **script** em [src\actions\provisionSample.YAML](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

    a. Na parte superior do ficheiro, adicione as linhas seguintes para temperatura, abaixo do comentário `// Add your sensor type here`:

    ```JavaScript
        var temperatureType = "Temperature";
        var temperatureThreshold = 78;
    ```

    b. Adicione as seguintes linhas após a instrução que define `var motionSensor`, abaixo do comentário `// Add your sensor variable here`:

     ```JavaScript
        var temperatureSensor = otherSensors.find(function(element) {
            return element.DataType === temperatureType;
        });
    ```

    c. Adicione a seguinte linha após a instrução que define `var carbonDioxideValue`, abaixo do comentário `// Add your sensor latest value here`:

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

1. Abra uma janela de comando e vá para a pasta **occupancy-quickstart\src**. Execute o seguinte comando para provisionar o grafo de inteligência espacial e a função definida pelo usuário:

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   > [!IMPORTANT]
   > Para impedir o acesso não autorizado à sua API de gerenciamento de gêmeos digital, o aplicativo **ocupação-início rápido** exige que você entre com suas credenciais de conta do Azure. Ele salva suas credenciais por um breve período, portanto, talvez você não precise entrar toda vez que executá-la. Na primeira vez em que este programa é executado e quando suas credenciais salvas expiram depois disso, o aplicativo o direciona para uma página de entrada e fornece um código específico da sessão para entrar nessa página. Siga os pedidos para iniciar sessão com a conta do Azure.

1. Depois que sua conta for autenticada, o aplicativo começará a criar um grafo espacial de exemplo conforme configurado em *provisionSample. YAML*. Aguarde até que o provisionamento seja concluído. Levará alguns minutos. Depois disso, observe as mensagens na janela de comando e observe como o grafo espacial é criado. Observe como o aplicativo cria um hub IoT no nó raiz ou no `Venue`.

1. Na saída na janela de comando, copie o valor de `ConnectionString`, `Devices` na seção, para a área de transferência. Você precisará desse valor para simular a conexão do dispositivo na próxima seção.

    ![Exemplo de aprovisionamento](./media/tutorial-facilities-udf/run-provision-sample.png)

> [!TIP]
> Se você receber uma mensagem de erro semelhante a "a operação de e/s foi anulada devido a uma saída de thread ou a uma solicitação de aplicativo" no meio do provisionamento, tente executar o comando novamente. Isso pode acontecer se o cliente HTTP esgotou o tempo limite de um problema de rede.

## <a name="simulate-sensor-data"></a>Simular dados do sensor

Nesta seção, você usará o projeto chamado *dispositivo-conectividade* no exemplo. Você simulará os dados do sensor para detectar o movimento, a temperatura e o dióxido de carbono. O projeto gera valores aleatórios para os sensores e envia-os para o hub IoT mediante a utilização da cadeia de ligação do dispositivo.

1. Em uma janela de comando separada, vá para o exemplo de gêmeos digital do Azure e, em seguida, para a pasta **dispositivo-conectividade** .

1. Execute este comando para confirmar que as dependências do projeto estão corretas:

    ```cmd/sh
    dotnet restore
    ```

1. Abra o arquivo [appSettings. JSON](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) em seu editor e edite os seguintes valores:

   a. **DeviceConnectionString**: Atribua o valor de `ConnectionString` na janela de saída da seção anterior. Copie essa cadeia de caracteres completamente, dentro das aspas, para que o simulador possa se conectar corretamente com o Hub IoT.

   b. **HardwareID** na matriz de **sensores** : Como você está simulando eventos de sensores provisionados para sua instância de gêmeos digital do Azure, a ID de hardware e os nomes dos sensores nesse arquivo devem corresponder ao `sensors` nó do arquivo *provisionSample. YAML* .

      Adicione uma nova entrada para o sensor de temperatura. O nó de **sensores** em *appSettings. JSON* deve ser semelhante ao seguinte:

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
   > Como o exemplo de simulação não se comunica diretamente com sua instância de gêmeos digital, ele não exige a autenticação.

## <a name="get-results-of-the-user-defined-function"></a>Obter resultados da função definida pelo usuário

A função definida pelo utilizador é executada sempre que a sua instância recebe dados de dispositivos e de sensores. Esta seção consulta sua instância de gêmeos digital do Azure para obter os resultados da função definida pelo usuário. Você verá quase em tempo real, quando uma sala estiver disponível, se o ar estiver atualizado e a temperatura estiver correta. 

1. Abra a janela de comando que você usou para provisionar o exemplo ou uma nova janela de comando e vá para a pasta **occupancy-quickstart\src** do exemplo novamente.

1. Execute o comando seguinte e inicie sessão quando lhe for pedido:

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

A janela saída mostra como a função definida pelo usuário é executada e intercepta eventos da simulação de dispositivo. 

   ![Saída para o UDF](./media/tutorial-facilities-udf/udf-running.png)

Se a condição monitorada for atendida, a função definida pelo usuário definirá o valor do espaço com a mensagem relevante, como vimos [anteriormente](#create-a-user-defined-function). A `GetAvailableAndFreshSpaces` função imprime a mensagem no console.

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender parar a explorar duplos Digital do Azure neste momento, fique à vontade eliminar recursos criados neste tutorial:

1. No menu do lado esquerdo da [portal do Azure](https://portal.azure.com), selecione **todos os recursos**, selecione o grupo de recursos digitais duplos e selecione **eliminar**.

    > [!TIP]
    > Se teve problemas ao eliminar a instância de duplos Digital, uma atualização de serviço capacidade foi implementada com a correção. Volte a tentar eliminar a instância.

2. Se necessário, exclua os aplicativos de exemplo em seu computador de trabalho.

## <a name="next-steps"></a>Passos seguintes

Agora que você provisionou seus espaços e criou uma estrutura para disparar notificações personalizadas, você pode ir para um dos seguintes tutoriais:

> [!div class="nextstepaction"]
> [Tutorial: Receber notificações de seus espaços de gêmeos digitais do Azure usando aplicativos lógicos](tutorial-facilities-events.md)

> [!div class="nextstepaction"]
> [Tutorial: Visualize e analise eventos de seus espaços de gêmeos digitais do Azure usando Time Series Insights](tutorial-facilities-analyze.md)
