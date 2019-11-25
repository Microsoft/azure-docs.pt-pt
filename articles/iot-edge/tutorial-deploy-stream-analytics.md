---
title: 'Tutorial: Stream Analytics at the edge - Azure IoT Edge'
description: In this tutorial, you deploy Azure Stream Analytics as a module to an IoT Edge device
author: kgremban
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 7fbbe32efcedd4fa2635db1cc21f7ce98557515b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452542"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>Tutorial: Deploy Azure Stream Analytics as an IoT Edge module

Many IoT solutions use analytics services to gain insight about data as it arrives in the cloud from IoT devices. Com o Azure IoT Edge, pode utilizar a lógica do [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) e movê-la para o dispositivo propriamente dito. Ao processar os fluxos de telemetria na periferia, pode reduzir a quantidade de dados carregados, bem como o tempo que demora a reagir a informações acionáveis.

O Azure IoT Edge e o Azure Stream Analytics estão integrados para que possa criar uma tarefa do Azure Stream Analytics no portal do Azure e, em seguida, implementá-la como um módulo do IoT Edge sem código adicional.  

Azure Stream Analytics provides a richly structured query syntax for data analysis, both in the cloud and on IoT Edge devices. For more information, see [Azure Stream Analytics documentation](../stream-analytics/stream-analytics-edge.md).

O módulo do Stream Analytics neste tutorial calcula a temperatura média num período de 30 segundos graduais. Quando essa média atingir 70, o módulo envia um alerta para o dispositivo tomar medidas. Neste caso, essa ação é repor o sensor de temperatura simulada. Num ambiente de produção, pode utilizar esta funcionalidade para encerrar uma máquina ou tomar medidas preventivas quando a temperatura atingir níveis perigosos. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma tarefa do Azure Stream Analytics para processar dados na periferia.
> * Ligue a nova tarefa do Azure Stream Analytics a outros módulos do IoT Edge.
> * Implemente a tarefa do Azure Stream Analytics num dispositivo IoT Edge a partir do portal do Azure.

<center>

![Diagram - Tutorial architecture, stage and deploy ASA job](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Azure IoT Edge:

* You can use an Azure virtual machine as an IoT Edge device by following the steps in the quickstart for [Linux](quickstart-linux.md) or [Windows devices](quickstart.md).

Recursos da cloud:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure. 


## <a name="create-an-azure-stream-analytics-job"></a>Criar uma tarefa do Azure Stream Analytics

In this section, you create an Azure Stream Analytics job that will do the following steps:
* Receive data from your IoT Edge device.
* Query the telemetry data for values outside a set range.
* Take action on the IoT Edge device based on the query results. 

### <a name="create-a-storage-account"></a>Create a storage account

Quando cria um trabalho do Azure Stream Analytics para ser executado num dispositivo IoT Edge, o trabalho tem de ser armazenado de forma a poder ser chamado a partir do dispositivo. Pode utilizar uma conta de armazenamento do Azure já existente ou criar uma nova agora. 

1. In the Azure portal, go to **Create a resource** > **Storage** > **Storage account**. 

1. Indique os valores seguintes para criar a conta de armazenamento:

   | Campo | Valor |
   | ----- | ----- |
   | Subscrição | Escolha a mesma subscrição do hub IoT. |
   | Grupo de recursos | Recomendamos que utilize o mesmo grupo de recursos para todos os recursos de teste que criou durante os inícios rápidos e tutoriais do IoT Edge. Por exemplo, **IoTEdgeResources**. |
   | Nome | Introduza um nome exclusivo para a conta de armazenamento. | 
   | Localização | Escolha uma localização perto de si. |


1. Keep the default values for the other fields and select **Review + Create**.

1. Review your settings then select **Create**.

### <a name="create-a-new-job"></a>Criar uma nova tarefa

1. In the Azure portal, go to **Create a resource** > **Internet of Things** > **Stream Analytics job**.

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

   ![Azure Stream Analytics - add input](./media/tutorial-deploy-stream-analytics/asa-input.png)

1. Escolha **Hub do Edge**, na lista pendente.

1. No painel **Nova entrada**, introduza **temperatura** como alias de entrada. 

1. Mantenha os valores predefinidos nos outros campos e selecione **Guardar**.

1. Em **Topologia do Trabalho**, abra **Saídas** e selecione **Adicionar**.

   ![Azure Stream Analytics - add output](./media/tutorial-deploy-stream-analytics/asa-output.png)

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

1. Under **Configure**, select **Storage account settings** then select **Add storage account**. 

   ![Azure Stream Analytics - add storage account](./media/tutorial-deploy-stream-analytics/add-storage-account.png)

1. Select the **Storage account** that you created at the beginning of this tutorial from the drop-down menu.

1. Para o campo **Contentor**, selecione **Criar novo** e forneça um nome para o contentor de armazenamento. 

1. Selecione **Guardar**. 

## <a name="deploy-the-job"></a>Implementar a tarefa

Está agora pronto para implementar a tarefa do Azure Stream Analytics no seu dispositivo IoT Edge. 

Nesta secção, vai utilizar o assistente **Definir Módulos**, no portal do Azure, para criar um *manifesto de implementação*. Os manifestos de implementação são ficheiros JSON que descrevem todos os módulos que vão ser implementados num dispositivo, os registos de contentor que armazenam as imagens dos módulos, de que forma é que os módulos devem ser geridos e como é que podem comunicar entre si. O dispositivo IoT Edge obtém o respetivo manifesto de implementação no Hub IoT e, em seguida, utiliza as informações contidas no mesmo para implementar e configurar todos os módulos que lhe foram atribuídos. 

Neste tutorial, vai implementar dois módulos. The first is **SimulatedTemperatureSensor**, which is a module that simulates a temperature and humidity sensor. O segundo é o seu trabalho do Stream Analytics. O módulo de sensor fornece o fluxo de dados que a consulta do trabalho vai analisar. 

1. No portal do Azure, navegue para o seu hub IoT.

1. Go to **IoT Edge**, and then open the details page for your IoT Edge device.

1. Selecione **Definir módulos**.  

1. If you previously deployed the SimulatedTemperatureSensor module on this device, it might autopopulate. Se não for o caso, adicione o módulo com os seguintes passos:

   1. Clique em **Adicionar** e selecione **Módulo do IoT Edge**.
   1. For the name, type **SimulatedTemperatureSensor**.
   1. Para o URI da imagem, introduza **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**. 
   1. Deixe as outras definições inalteradas e selecione **Guardar**.

1. Adicione a tarefa do Azure Stream Analytics Edge com os seguintes passos:

   1. Clique em **Adicionar** e selecione **Módulo do Azure Stream Analytics**.
   1. Selecione a sua subscrição e a tarefa do Azure Stream Analytics Edge que criou. 
   1. Selecione **Guardar**.

   Once you save your changes, the details of your Stream Analytics job are published to the storage container that you created. 

1. When the Stream Analytics module is added to the list of modules, select **Configure** to see how it's structured. 

   O URI da imagem aponta para uma imagem padrão do Azure Stream Analytics. This one image is used for every Stream Analytics module that gets deployed to an IoT Edge device. 

   O módulo duplo é configurado com uma propriedade pretendida, denominada **ASAJobInfo**, cujo valor aponta para a definição do trabalho no seu contentor de armazenamento. This property is how the Stream Analytics image is configured with your specific job details. 

   By default, the Stream Analytics module takes the same name as the job it's based on. You can change the module name on this page if you like, but it's not necessary. 

1. Close the module configuration page.

1. Aponte o nome do módulo do Stream Analytics, porque vai precisar do mesmo no próximo passo, e selecione **Seguinte**, para continuar.

1. Substitua o valor predefinido em **Rotas** pelo código seguinte. Atualize as três instâncias de _{moduleName}_ com o nome do módulo do Azure Stream Analytics. 

    ```json
    {
        "routes": {
            "telemetryToCloud": "FROM /messages/modules/SimulatedTemperatureSensor/* INTO $upstream",
            "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream",
            "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/SimulatedTemperatureSensor/inputs/control\")",
            "telemetryToAsa": "FROM /messages/modules/SimulatedTemperatureSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")"
        }
    }
    ```

   As rotas que declarar aqui definem o fluxo dos dados através do dispositivo IoT Edge. The telemetry data from SimulatedTemperatureSensor are sent to IoT Hub and to the **temperature** input that was configured in the Stream Analytics job. The **alert** output messages are sent to IoT Hub and to the SimulatedTemperatureSensor module to trigger the reset command. 

1. Selecione **Seguinte**.

1. In the **Review Deployment** step, you can see how the information you provided in the wizard is converted into a JSON deployment manifest. When you're done reviewing the manifest, select **Submit**.

1. Regresse à página de detalhes do dispositivo e, em seguida, selecione **Atualizar**.  

    You should see the new Stream Analytics module running, along with the IoT Edge agent and IoT Edge hub modules. It may take a few minutes for the information to reach your IoT Edge device, and then for the new modules to start. If you don't see the modules running right away, continue refreshing the page.

    ![SimulatedTemperatureSensor and ASA module reported by device](./media/tutorial-deploy-stream-analytics/module-output2.png)

## <a name="view-data"></a>Ver dados

Now you can go to your IoT Edge device to check out the interaction between the Azure Stream Analytics module and the SimulatedTemperatureSensor module.

1. Verifique se todos os módulos estão em execução no Docker:

   ```cmd/sh
   iotedge list  
   ```
   <!--
   ![Docker output](./media/tutorial-deploy-stream-analytics/docker_output.png)
   -->
1. Veja todos os dados de métricas e registos de sistema. Utilize o nome do módulo do Stream Analytics:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

1. View the reset command affect the SimulatedTemperatureSensor by viewing the sensor logs:

   ```cmd/sh
   iotedge logs SimulatedTemperatureSensor
   ```

   You can watch the machine's temperature gradually rise until it reaches 70 degrees for 30 seconds. Em seguida, o módulo do Stream Analytics aciona uma reposição e a temperatura da máquina baixa novamente para 21. 

   ![Reset command output into module logs](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>Limpar recursos 

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Otherwise, you can delete the local configurations and the Azure resources that you used in this article to avoid charges. 
 
[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurou uma tarefa do Azure Stream Analytics para analisar dados a partir do dispositivo IoT Edge. Em seguida, carregou este módulo do Azure Stream Analytics no dispositivo do IoT Edge para processar e reagir ao aumento da temperatura localmente, bem como enviar o fluxo de dados agregados para a cloud. Para ver de que forma o Azure IoT Edge pode criar mais soluções para a sua empresa, avance para os outros tutoriais.

> [!div class="nextstepaction"] 
> [Implementar um modelo do Azure Machine Learning como um módulo](tutorial-deploy-machine-learning.md)
