---
title: Tutorial de implementar uma tarefa do Azure Stream Analytics num dispositivo - Azure IoT Edge | Documentos da Microsoft
description: Neste tutorial, vai implementar o Azure Stream Analytics como um módulo num dispositivo IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 31330c3d2b10e6245db775da4039fd1948539df4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58106020"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>Tutorial: Implementar o Azure Stream Analytics como um módulo do IoT Edge

Muitas soluções de IoT utilizam os serviços de análise para obter informações sobre os dados à medida que são recebidos na cloud a partir dos dispositivos IoT. Com o Azure IoT Edge, pode utilizar a lógica do [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) e movê-la para o dispositivo propriamente dito. Ao processar os fluxos de telemetria na periferia, pode reduzir a quantidade de dados carregados, bem como o tempo que demora a reagir a informações acionáveis.

O Azure IoT Edge e o Azure Stream Analytics estão integrados para que possa criar uma tarefa do Azure Stream Analytics no portal do Azure e, em seguida, implementá-la como um módulo do IoT Edge sem código adicional.  

O Azure Stream Analytics fornece uma sintaxe de consulta altamente estruturada para análise de dados na cloud e em dispositivos IoT Edge. Para obter mais informações sobre o Azure Stream Analytics no IoT Edge, veja a [documentação do Azure Stream Analytics](../stream-analytics/stream-analytics-edge.md).

O módulo do Stream Analytics neste tutorial calcula a temperatura média num período de 30 segundos graduais. Quando essa média atingir 70, o módulo envia um alerta para o dispositivo tomar medidas. Neste caso, essa ação é repor o sensor de temperatura simulada. Num ambiente de produção, pode utilizar esta funcionalidade para encerrar uma máquina ou tomar medidas preventivas quando a temperatura atingir níveis perigosos. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma tarefa do Azure Stream Analytics para processar dados na periferia.
> * Ligue a nova tarefa do Azure Stream Analytics a outros módulos do IoT Edge.
> * Implemente a tarefa do Azure Stream Analytics num dispositivo IoT Edge a partir do portal do Azure.

<center>

![Diagrama - arquitetura do Tutorial, testar e implementar a tarefa ASA](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Azure IoT Edge:

* Pode seguir os passos no início rápido para [dispositivos Linux](quickstart-linux.md) ou [Windows](quickstart.md) para utilizar o seu computador de desenvolvimento ou uma máquina virtual como um dispositivo Edge.

Recursos da cloud:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure. 


## <a name="create-an-azure-stream-analytics-job"></a>Criar uma tarefa do Azure Stream Analytics

Nesta secção, vai criar uma tarefa do Azure Stream Analytics para utilizar os dados do seu hub IoT, consultar os dados de telemetria enviados do seu dispositivo e, em seguida, reencaminhar os resultados para um contentor de armazenamento de Blobs do Azure. 

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Quando cria um trabalho do Azure Stream Analytics para ser executado num dispositivo IoT Edge, o trabalho tem de ser armazenado de forma a poder ser chamado a partir do dispositivo. Pode utilizar uma conta de armazenamento do Azure já existente ou criar uma nova agora. 

1. No portal do Azure, aceda a **Criar um recurso** > **Armazenamento** > **Conta de armazenamento - blob, ficheiro, tabela, fila**. 

1. Indique os valores seguintes para criar a conta de armazenamento:

   | Campo | Valor |
   | ----- | ----- |
   | Nome | Introduza um nome exclusivo para a conta de armazenamento. | 
   | Localização | Escolha uma localização perto de si. |
   | Subscrição | Escolha a mesma subscrição do hub IoT. |
   | Grupo de recursos | Recomendamos que utilize o mesmo grupo de recursos para todos os recursos de teste que criou durante os inícios rápidos e tutoriais do IoT Edge. Por exemplo, **IoTEdgeResources**. |

1. Mantenha os valores predefinidos nos outros campos e selecione **Criar**. 

### <a name="create-a-new-job"></a>Criar uma nova tarefa

1. No portal do Azure, aceda a **Criar um recurso** > **Internet das Coisas** > **Trabalho do Stream Analytics**.

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

   ![O Azure Stream Analytics Adicionar entrada](./media/tutorial-deploy-stream-analytics/asa_input.png)

1. Escolha **Hub do Edge**, na lista pendente.

1. No painel **Nova entrada**, introduza **temperatura** como alias de entrada. 

1. Mantenha os valores predefinidos nos outros campos e selecione **Guardar**.

1. Em **Topologia do Trabalho**, abra **Saídas** e selecione **Adicionar**.

   ![O Azure Stream Analytics adicionar saída](./media/tutorial-deploy-stream-analytics/asa_output.png)

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

1. Em **Configurar**, selecione **Definições da conta de armazenamento**.

1. Selecione **Adicionar conta de armazenamento**. 

1. Selecione a **Conta de armazenamento** no menu pendente.

1. Para o campo **Contentor**, selecione **Criar novo** e forneça um nome para o contentor de armazenamento. 

1. Selecione **Guardar**. 

## <a name="deploy-the-job"></a>Implementar a tarefa

Está agora pronto para implementar a tarefa do Azure Stream Analytics no seu dispositivo IoT Edge. 

Nesta secção, vai utilizar o assistente **Definir Módulos**, no portal do Azure, para criar um *manifesto de implementação*. Os manifestos de implementação são ficheiros JSON que descrevem todos os módulos que vão ser implementados num dispositivo, os registos de contentor que armazenam as imagens dos módulos, de que forma é que os módulos devem ser geridos e como é que podem comunicar entre si. O dispositivo IoT Edge obtém o respetivo manifesto de implementação no Hub IoT e, em seguida, utiliza as informações contidas no mesmo para implementar e configurar todos os módulos que lhe foram atribuídos. 

Neste tutorial, vai implementar dois módulos. O primeiro é **tempSensor**, que é um módulo que simula um sensor de temperatura e humidade. O segundo é o seu trabalho do Stream Analytics. O módulo de sensor fornece o fluxo de dados que a consulta do trabalho vai analisar. 

1. No portal do Azure, no hub IoT, aceda a **IoT Edge** e, em seguida, abra a página de detalhes do dispositivo IoT Edge.

1. Selecione **Definir módulos**.  

1. Se já implementou o módulo tempSensor neste dispositivo, este poderá ser automaticamente populado. Se não for o caso, adicione o módulo com os seguintes passos:

   1. Clique em **Adicionar** e selecione **Módulo do IoT Edge**.
   1. Para o nome, escreva **tempSensor**.
   1. Para o URI da imagem, introduza **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**. 
   1. Deixe as outras definições inalteradas e selecione **Guardar**.

1. Adicione a tarefa do Azure Stream Analytics Edge com os seguintes passos:

   1. Clique em **Adicionar** e selecione **Módulo do Azure Stream Analytics**.
   1. Selecione a sua subscrição e a tarefa do Azure Stream Analytics Edge que criou. 
   1. Selecione **Guardar**.

1. Quando o trabalho do Stream Analytics for publicado no contentor de armazenamento que criou, clique no nome do módulo para ver a estrutura dos módulos do Stream Analytics. 

   O URI da imagem aponta para uma imagem padrão do Azure Stream Analytics. Esta é a mesma imagem utilizada para todos os trabalhos que são implementados num dispositivo IoT Edge. 

   O módulo duplo é configurado com uma propriedade pretendida, denominada **ASAJobInfo**, cujo valor aponta para a definição do trabalho no seu contentor de armazenamento. É com esta propriedade que a imagem do Stream Analytics é configurada com as informações do trabalho específicas. 

1. Feche a página do módulo.

1. Aponte o nome do módulo do Stream Analytics, porque vai precisar do mesmo no próximo passo, e selecione **Seguinte**, para continuar.

1. Substitua o valor predefinido em **Rotas** pelo código seguinte. Atualize as três instâncias de _{moduleName}_ com o nome do módulo do Azure Stream Analytics. 

    ```json
    {
        "routes": {
            "telemetryToCloud": "FROM /messages/modules/tempSensor/* INTO $upstream",
            "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream",
            "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")",
            "telemetryToAsa": "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")"
        }
    }
    ```

   As rotas que declarar aqui definem o fluxo dos dados através do dispositivo IoT Edge. Os dados de telemetria de tempSensor são enviados para o Hub IoT e para a entrada **temperature** que foi configurada no trabalho do Stream Analytics. As mensagens da saída de **alert** são enviadas para o Hub IoT e para o módulo tempSensor, para acionar o comando de reposição. 

1. Selecione **Seguinte**.

1. No passo **Rever Implementação**, selecione **Submeter**.

1. Regresse à página de detalhes do dispositivo e, em seguida, selecione **Atualizar**.  

    Deverá ver o novo módulo do Stream Analytics em execução, juntamente com o módulo do agente do IoT Edge e o hub do IoT Edge.

    ![tempSensor e módulo ASA comunicada pelo dispositivo](./media/tutorial-deploy-stream-analytics/module_output2.png)

## <a name="view-data"></a>Ver dados

Agora, pode aceder ao dispositivo IoT Edge para verificar a interação entre o módulo do Azure Stream Analytics e o módulo tempSensor.

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

Deverá conseguir ver a temperatura da máquina a aumentar gradualmente até atingir 70 graus durante 30 segundos. Em seguida, o módulo do Stream Analytics aciona uma reposição e a temperatura da máquina baixa novamente para 21. 

   ![Repor a saída do comando sobre os registos do módulo](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>Limpar recursos 

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, pode eliminar as configurações locais e os recursos do Azure que criou neste artigo para evitar custos. 
 
[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, configurou uma tarefa do Azure Stream Analytics para analisar dados a partir do dispositivo IoT Edge. Em seguida, carregou este módulo do Azure Stream Analytics no dispositivo do IoT Edge para processar e reagir ao aumento da temperatura localmente, bem como enviar o fluxo de dados agregados para a cloud. Para ver de que forma o Azure IoT Edge pode criar mais soluções para a sua empresa, avance para os outros tutoriais.

> [!div class="nextstepaction"] 
> [Implementar um modelo do Azure Machine Learning como um módulo](tutorial-deploy-machine-learning.md)
