---
title: Tutorial - Stream Analytics na borda usando Azure IoT Edge
description: Neste tutorial, você implementa o Azure Stream Analytics como um módulo para um dispositivo IoT Edge
author: kgremban
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: d8c3bde0f32c1df6c98f6a71f6ab830c21256903
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76906285"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>Tutorial: Implemente o Azure Stream Analytics como módulo IoT Edge

Muitas soluções IoT usam serviços de análise para obter informações sobre dados à medida que chegam à nuvem a partir de dispositivos IoT. Com o Azure IoT Edge, pode utilizar a lógica do [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) e movê-la para o dispositivo propriamente dito. Ao processar os fluxos de telemetria na periferia, pode reduzir a quantidade de dados carregados, bem como o tempo que demora a reagir a informações acionáveis.

Azure IoT Edge e Azure Stream Analytics estão integrados para simplificar o seu desenvolvimento de carga de trabalho. Pode criar um trabalho de Azure Stream Analytics no portal Azure e depois implantá-lo como um módulo IoT Edge sem código adicional.  

O Azure Stream Analytics fornece uma sintaxe de consulta ricamente estruturada para análise de dados, tanto na nuvem como nos dispositivos IoT Edge. Para mais informações, consulte a [documentação do Azure Stream Analytics.](../stream-analytics/stream-analytics-edge.md)

O módulo do Stream Analytics neste tutorial calcula a temperatura média num período de 30 segundos graduais. Quando essa média atingir 70, o módulo envia um alerta para o dispositivo tomar medidas. Neste caso, essa ação é repor o sensor de temperatura simulada. Num ambiente de produção, pode utilizar esta funcionalidade para encerrar uma máquina ou tomar medidas preventivas quando a temperatura atingir níveis perigosos.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Crie uma tarefa do Azure Stream Analytics para processar dados na periferia.
> * Ligue a nova tarefa do Azure Stream Analytics a outros módulos do IoT Edge.
> * Implemente a tarefa do Azure Stream Analytics num dispositivo IoT Edge a partir do portal do Azure.

<center>

![Diagrama - Arquitetura tutorial: estágio e implantação de trabalho asa](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Azure IoT Edge:

* Pode utilizar uma máquina virtual Azure como um dispositivo IoT Edge seguindo os passos no arranque rápido para [dispositivos](quickstart.md) [Linux](quickstart-linux.md) ou Windows .

Recursos da cloud:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.

## <a name="create-an-azure-stream-analytics-job"></a>Criar uma tarefa do Azure Stream Analytics

Nesta secção, cria-se um trabalho de Azure Stream Analytics que fará os seguintes passos:

* Receba dados do seu dispositivo IoT Edge.
* Consultar os dados de telemetria para valores fora de um intervalo definido.
* Tome medidas no dispositivo IoT Edge com base nos resultados da consulta.

### <a name="create-a-storage-account"></a>Criar uma conta do Storage

Quando cria um trabalho do Azure Stream Analytics para ser executado num dispositivo IoT Edge, o trabalho tem de ser armazenado de forma a poder ser chamado a partir do dispositivo. Pode utilizar uma conta de Armazenamento Azure existente, ou criar uma nova agora.

1. No portal Azure, vá criar uma**conta**de**armazenamento** > de **recursos.** > 

1. Indique os valores seguintes para criar a conta de armazenamento:

   | Campo | Valor |
   | ----- | ----- |
   | Subscrição | Escolha a mesma subscrição do hub IoT. |
   | Grupo de recursos | Recomendamos que utilize o mesmo grupo de recursos para todos os seus recursos de teste para os quickstarts e tutoriais IoT Edge. Por exemplo, **IoTEdgeResources**. |
   | Nome | Introduza um nome exclusivo para a conta de armazenamento. |
   | Localização | Escolha uma localização perto de si. |

1. Mantenha os valores predefinidos para os outros campos e selecione **Review + Create**.

1. Reveja as definições e selecione **Criar**.

### <a name="create-a-new-job"></a>Criar uma nova tarefa

1. No portal Azure, vá criar **um recurso** > **Internet of Things** > **Stream Analytics.**

1. Indique os valores seguintes para criar o trabalho:

   | Campo | Valor |
   | ----- | ----- |
   | Nome da tarefa | Indique um nome para o trabalho. Por exemplo, **IoTEdgeJob** |
   | Subscrição | Escolha a mesma subscrição do hub IoT. |
   | Grupo de recursos | Recomendamos que utilize o mesmo grupo de recursos para todos os recursos de teste que criou durante os inícios rápidos e tutoriais do IoT Edge. Por exemplo, **IoTEdgeResources**. |
   | Localização | Escolha uma localização perto de si. |
   | Ambiente de alojamento | Selecione **Edge**. |

1. Selecione **Criar**.

### <a name="configure-your-job"></a>Configurar o trabalho

Quando o trabalho do Stream Analytics estiver criado no portal do Azure, pode configurá-lo com uma entrada, uma saída e uma consulta para ser executada nos dados que são transmitidos.

Ao utilizar os três elementos, entrada, saída e consulta, esta secção cria um trabalho que recebe dados de temperatura do dispositivo IoT Edge. Analisa esses dados numa janela de 30 segundos sem interrupção. Se a temperatura média nessa janela exceder os 70 graus, é enviado um alerta para o dispositivo IoT Edge. Na próxima secção, quando implementar o trabalho, vai especificar exatamente de onde os dados vêm e para onde vão.  

1. Navegue para o trabalho do Stream Analytics no portal do Azure.

1. Em **Topologia do Trabalho**, selecione **Entradas** e **Adicionar entrada de fluxo**.

   ![Azure Stream Analytics - adicione entrada](./media/tutorial-deploy-stream-analytics/asa-input.png)

1. Escolha **Hub do Edge**, na lista pendente.

1. No painel **Nova entrada**, introduza **temperatura** como alias de entrada.

1. Mantenha os valores predefinidos nos outros campos e selecione **Guardar**.

1. Em **Topologia do Trabalho**, abra **Saídas** e selecione **Adicionar**.

   ![Azure Stream Analytics - adicione a saída](./media/tutorial-deploy-stream-analytics/asa-output.png)

1. Escolha **Hub do Edge**, na lista pendente.

1. No painel **Nova saída**, introduza **alerta** como alias de saída.

1. Mantenha os valores predefinidos nos outros campos e selecione **Guardar**.

1. Em **Topologia do Trabalho**, selecione **Consulta**.

1. Substitua o texto predefinido pela seguinte consulta. Se a temperatura média da máquina numa janela de 30 segundos atingir os 70 graus, o código SQL envia um comando de reposição para a saída do alerta. O comando de reposição foi pré-programado no sensor como uma ação que pode ser realizada.

    ```sql
    SELECT  
        'reset' AS command
    INTO
       alert
    FROM
       temperature TIMESTAMP BY timeCreated
    GROUP BY TumblingWindow(second,30)
    HAVING Avg(machine.temperature) > 70
    ```

1. Selecione **Guardar**.

### <a name="configure-iot-edge-settings"></a>Configurar as definições do IoT Edge

Para preparar a implementação do trabalho do Stream Analytics num dispositivo IoT Edge, tem de associá-lo a um contentor numa conta de armazenamento. Quando implementa o trabalho, a definição do mesmo é exportada para o contentor de armazenamento.

1. Em **configurar, selecione** **as definições da conta de depósito** e selecione adicionar a conta de **armazenamento**.

   ![Azure Stream Analytics - adicionar conta de armazenamento](./media/tutorial-deploy-stream-analytics/add-storage-account.png)

1. Selecione a **conta de Armazenamento** que criou no início deste tutorial a partir do menu suspenso.

1. Para o campo **Contentor**, selecione **Criar novo** e forneça um nome para o contentor de armazenamento.

1. Selecione **Guardar**.

## <a name="deploy-the-job"></a>Implementar a tarefa

Está agora pronto para implementar a tarefa do Azure Stream Analytics no seu dispositivo IoT Edge.

Nesta secção, vai utilizar o assistente **Definir Módulos**, no portal do Azure, para criar um *manifesto de implementação*. Os manifestos de implementação são ficheiros JSON que descrevem todos os módulos que vão ser implementados num dispositivo, os registos de contentor que armazenam as imagens dos módulos, de que forma é que os módulos devem ser geridos e como é que podem comunicar entre si. O dispositivo IoT Edge obtém o respetivo manifesto de implementação no Hub IoT e, em seguida, utiliza as informações contidas no mesmo para implementar e configurar todos os módulos que lhe foram atribuídos.

Neste tutorial, vai implementar dois módulos. O primeiro é **SimuladoSensor de Temperatura,** que é um módulo que simula um sensor de temperatura e humidade. O segundo é o seu trabalho do Stream Analytics. O módulo de sensor fornece o fluxo de dados que a consulta do trabalho vai analisar.

1. No portal do Azure, navegue para o seu hub IoT.

1. Vá ao **IoT Edge**e abra a página de detalhes para o seu dispositivo IoT Edge.

1. Selecione **Definir módulos**.  

1. Se tiver implantado previamente o módulo SimuladoSensor de Temperatura neste dispositivo, poderá autopovoar-se. Se não for o caso, adicione o módulo com os seguintes passos:

   1. Clique em **Adicionar** e selecione **Módulo do IoT Edge**.
   1. Para o nome, **escreva Sensor de Temperatura Simulado**.
   1. Para o URI da imagem, introduza **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**.
   1. Deixe as outras definições inalteradas e selecione **Guardar**.

1. Adicione a tarefa do Azure Stream Analytics Edge com os seguintes passos:

   1. Clique em **Adicionar** e selecione **Módulo do Azure Stream Analytics**.
   1. Selecione a sua subscrição e a tarefa do Azure Stream Analytics Edge que criou.
   1. Selecione **Guardar**.

   Assim que guardar as suas alterações, os detalhes do seu trabalho em Stream Analytics são publicados no recipiente de armazenamento que criou.

1. Quando o módulo Stream Analytics for adicionado à lista de módulos, selecione o seu nome para ver como é estruturado e atualize as suas definições na página do **Módulo De Ponta Do Atualização IoT.**

   O separador **Definições** de Módulo tem a **imagem URI** que aponta para uma imagem padrão do Azure Stream Analytics. Esta imagem é usada para cada módulo Stream Analytics que é implantado num dispositivo IoT Edge.

   O separador **Definições Twin Module** mostra o JSON que define a propriedade Azure Stream Analytics (ASA) chamada **ASAJobInfo**. cujo valor aponta para a definição do trabalho no seu contentor de armazenamento. Esta propriedade é como a imagem stream Analytics é configurada com os seus detalhes específicos do trabalho.

   Por padrão, o módulo Stream Analytics tem o mesmo nome que o trabalho em que se baseia. Pode alterar o nome do módulo nesta página, se quiser, mas não é necessário.

1. Selecione **Cancelar** ou **Guardar**.

1. Tome nota do nome do seu módulo Stream Analytics porque vai precisar dele no próximo passo e, em seguida, selecione **Next: Routes** to continue.

1. No separador **Rotas,** define como as mensagens são passadas entre os módulos e o IoT Hub. As mensagens são construídas utilizando pares de nome/valor. Substitua `route` o `upstream` padrão e o nome e os valores com os pares apresentados na tabela seguinte, o seguinte nome/valor pares, substituindo as instâncias de _{moduleName}_ pelo nome do seu módulo Azure Stream Analytics.

    | Nome | Valor |
    | --- | --- |
    | `telemetryToCloud` | `FROM /messages/modules/SimulatedTemperatureSensor/* INTO $upstream` |
    | `alertsToCloud` | `FROM /messages/modules/{moduleName}/* INTO $upstream` |
    | `alertsToReset` | `FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint("/modules/SimulatedTemperatureSensor/inputs/control")` |
    | `telemetryToAsa` | `FROM /messages/modules/SimulatedTemperatureSensor/* INTO BrokeredEndpoint("/modules/{moduleName}/inputs/temperature")`|

    As rotas que declarar aqui definem o fluxo dos dados através do dispositivo IoT Edge. Os dados de telemetria do Sensor de Temperatura Simulado são enviados para o IoT Hub e para a entrada de **temperatura** que foi configurada no trabalho stream analytics. As mensagens de saída de **alerta** são enviadas para o Hub IoT e para o módulo SimulatedTemperatureSensor para acionar o comando de reset.

1. Selecione **A seguir: Rever + Criar**.

1. No separador **Review + Criar,** pode ver como a informação fornecida no assistente é convertida num manifesto de implementação JSON. Quando terminar de rever o manifesto, selecione **Criar**.

1. Está sossetado na página de detalhes do dispositivo. Selecione **Refresh**.  

    Deve ver o novo módulo Stream Analytics a funcionar, juntamente com o agente IoT Edge e os módulos hub IoT Edge. Pode levar alguns minutos para que a informação chegue ao seu dispositivo IoT Edge e, em seguida, para que os novos módulos comecem. Se não vir os módulos a correr imediatamente, continue a refrescar a página.

    ![Módulo SimuladoSensor de Temperatura e ASA reportado saca](./media/tutorial-deploy-stream-analytics/module-output2.png)

## <a name="view-data"></a>Ver dados

Agora pode ir ao seu dispositivo IoT Edge para verificar a interação entre o módulo Azure Stream Analytics e o módulo SimuladoTemperatureSensor.

1. Verifique se todos os módulos estão em execução no Docker:

   ```cmd/sh
   iotedge list  
   ```

1. Veja todos os dados de métricas e registos de sistema. Utilize o nome do módulo do Stream Analytics:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

1. Ver o comando de reset afetar o Sensor de Temperatura Simulado visualizando os registos do sensor:

   ```cmd/sh
   iotedge logs SimulatedTemperatureSensor
   ```

   Pode ver a temperatura da máquina subir gradualmente até atingir os 70 graus durante 30 segundos. Em seguida, o módulo do Stream Analytics aciona uma reposição e a temperatura da máquina baixa novamente para 21.

   ![Redefinir a saída de comando em registos de módulos](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, pode eliminar as configurações locais e os recursos Azure que utilizou neste artigo para evitar encargos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurou uma tarefa do Azure Stream Analytics para analisar dados a partir do dispositivo IoT Edge. Em seguida, carregou este módulo do Azure Stream Analytics no dispositivo do IoT Edge para processar e reagir ao aumento da temperatura localmente, bem como enviar o fluxo de dados agregados para a cloud. Para ver de que forma o Azure IoT Edge pode criar mais soluções para a sua empresa, avance para os outros tutoriais.

> [!div class="nextstepaction"]
> [Implementar o modelo do Azure Machine Learning como um módulo](tutorial-deploy-machine-learning.md)
