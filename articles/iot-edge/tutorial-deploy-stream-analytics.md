---
title: Tutorial-Stream Analytics na borda usando Azure IoT Edge
description: Neste tutorial, você implanta Azure Stream Analytics como um módulo para um dispositivo IoT Edge
author: kgremban
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 648eb6cdb1787e1cbdf82bd8e5c8499b0dbaf02c
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772261"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>Tutorial: implantar Azure Stream Analytics como um módulo IoT Edge

Muitas soluções de IoT usam o Analytics Services para obter informações sobre os dados conforme chegam na nuvem de dispositivos IoT. Com o Azure IoT Edge, pode utilizar a lógica do [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) e movê-la para o dispositivo propriamente dito. Ao processar os fluxos de telemetria na periferia, pode reduzir a quantidade de dados carregados, bem como o tempo que demora a reagir a informações acionáveis.

Azure IoT Edge e Azure Stream Analytics estão integrados para simplificar o seu desenvolvimento de carga de trabalho. Pode criar um trabalho de Azure Stream Analytics no portal Azure e depois implantá-lo como um módulo IoT Edge sem código adicional.  

Azure Stream Analytics fornece uma sintaxe de consulta estruturada sofisticada para análise de dados, tanto na nuvem quanto em dispositivos IoT Edge. Para obter mais informações, consulte a [documentação do Azure Stream Analytics](../stream-analytics/stream-analytics-edge.md).

O módulo do Stream Analytics neste tutorial calcula a temperatura média num período de 30 segundos graduais. Quando essa média atingir 70, o módulo envia um alerta para o dispositivo tomar medidas. Neste caso, essa ação é repor o sensor de temperatura simulada. Num ambiente de produção, pode utilizar esta funcionalidade para encerrar uma máquina ou tomar medidas preventivas quando a temperatura atingir níveis perigosos.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Crie uma tarefa do Azure Stream Analytics para processar dados na periferia.
> * Ligue a nova tarefa do Azure Stream Analytics a outros módulos do IoT Edge.
> * Implemente a tarefa do Azure Stream Analytics num dispositivo IoT Edge a partir do portal do Azure.

<center>

Diagrama ![- Arquitetura tutorial: palco e implantação de trabalho da ASA](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Azure IoT Edge:

* Você pode usar uma máquina virtual do Azure como um dispositivo IoT Edge seguindo as etapas no guia de início rápido para [dispositivos](quickstart.md) [Linux](quickstart-linux.md) ou Windows.

Recursos da cloud:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.

## <a name="create-an-azure-stream-analytics-job"></a>Criar uma tarefa do Azure Stream Analytics

Nesta seção, você cria um trabalho de Azure Stream Analytics que executará as seguintes etapas:

* Receber dados de seu dispositivo IoT Edge.
* Consultar os dados de telemetria para obter valores fora de um intervalo definido.
* Tome medidas no dispositivo IoT Edge com base nos resultados da consulta.

### <a name="create-a-storage-account"></a>Create a storage account

Quando cria um trabalho do Azure Stream Analytics para ser executado num dispositivo IoT Edge, o trabalho tem de ser armazenado de forma a poder ser chamado a partir do dispositivo. Pode utilizar uma conta de Armazenamento Azure existente, ou criar uma nova agora.

1. Na portal do Azure, vá para **criar um recurso** > **armazenamento** > **conta de armazenamento**.

1. Indique os valores seguintes para criar a conta de armazenamento:

   | Campo | Valor |
   | ----- | ----- |
   | Subscrição | Escolha a mesma subscrição do hub IoT. |
   | Grupo de recursos | Recomendamos que utilize o mesmo grupo de recursos para todos os seus recursos de teste para os quickstarts e tutoriais IoT Edge. Por exemplo, **IoTEdgeResources**. |
   | Nome | Introduza um nome exclusivo para a conta de armazenamento. |
   | Localização | Escolha uma localização perto de si. |

1. Mantenha os valores padrão para os outros campos e selecione **revisar + criar**.

1. Examine suas configurações e, em seguida, selecione **criar**.

### <a name="create-a-new-job"></a>Criar uma nova tarefa

1. Na portal do Azure, vá para **criar um recurso** > **Internet das coisas** > **trabalho de Stream Analytics**.

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

   ![Azure Stream Analytics-Adicionar entrada](./media/tutorial-deploy-stream-analytics/asa-input.png)

1. Escolha **Hub do Edge**, na lista pendente.

1. No painel **Nova entrada**, introduza **temperatura** como alias de entrada.

1. Mantenha os valores predefinidos nos outros campos e selecione **Guardar**.

1. Em **Topologia do Trabalho**, abra **Saídas** e selecione **Adicionar**.

   ![Azure Stream Analytics-adicionar saída](./media/tutorial-deploy-stream-analytics/asa-output.png)

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

1. Em **Configurar**, selecione **configurações de conta de armazenamento** e, em seguida, selecione **adicionar conta de armazenamento**.

   ![Azure Stream Analytics-adicionar conta de armazenamento](./media/tutorial-deploy-stream-analytics/add-storage-account.png)

1. Selecione a **conta de armazenamento** que você criou no início deste tutorial no menu suspenso.

1. Para o campo **Contentor**, selecione **Criar novo** e forneça um nome para o contentor de armazenamento.

1. Selecione **Guardar**.

## <a name="deploy-the-job"></a>Implementar a tarefa

Está agora pronto para implementar a tarefa do Azure Stream Analytics no seu dispositivo IoT Edge.

Nesta secção, vai utilizar o assistente **Definir Módulos**, no portal do Azure, para criar um *manifesto de implementação*. Os manifestos de implementação são ficheiros JSON que descrevem todos os módulos que vão ser implementados num dispositivo, os registos de contentor que armazenam as imagens dos módulos, de que forma é que os módulos devem ser geridos e como é que podem comunicar entre si. O dispositivo IoT Edge obtém o respetivo manifesto de implementação no Hub IoT e, em seguida, utiliza as informações contidas no mesmo para implementar e configurar todos os módulos que lhe foram atribuídos.

Neste tutorial, vai implementar dois módulos. O primeiro é **SimulatedTemperatureSensor**, que é um módulo que simula um sensor de temperatura e umidade. O segundo é o seu trabalho do Stream Analytics. O módulo de sensor fornece o fluxo de dados que a consulta do trabalho vai analisar.

1. No portal do Azure, navegue para o seu hub IoT.

1. Vá para **IOT Edge**e, em seguida, abra a página de detalhes de seu dispositivo de IOT Edge.

1. Selecione **Definir módulos**.  

1. Se você tiver implantado anteriormente o módulo SimulatedTemperatureSensor neste dispositivo, ele poderá preencher automaticamente. Se não for o caso, adicione o módulo com os seguintes passos:

   1. Clique em **Adicionar** e selecione **Módulo do IoT Edge**.
   1. Para o nome, digite **SimulatedTemperatureSensor**.
   1. Para o URI da imagem, introduza **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**.
   1. Deixe as outras definições inalteradas e selecione **Guardar**.

1. Adicione a tarefa do Azure Stream Analytics Edge com os seguintes passos:

   1. Clique em **Adicionar** e selecione **Módulo do Azure Stream Analytics**.
   1. Selecione a sua subscrição e a tarefa do Azure Stream Analytics Edge que criou.
   1. Selecione **Guardar**.

   Depois de salvar as alterações, os detalhes de seu trabalho de Stream Analytics são publicados no contêiner de armazenamento que você criou.

1. Quando o módulo Stream Analytics for adicionado à lista de módulos, selecione seu nome para ver como ele é estruturado e atualize suas configurações na página do **módulo atualizar IOT Edge** .

   A guia **configurações do módulo** tem o URI da **imagem** que aponta para uma imagem de Azure Stream Analytics padrão. Essa imagem é usada para cada módulo de Stream Analytics que é implantado em um dispositivo IoT Edge.

   A guia **configurações de configuração do módulo** mostra o JSON que define a propriedade Azure Stream Analytics (asa) chamada **ASAJobInfo**. cujo valor aponta para a definição do trabalho no seu contentor de armazenamento. Essa propriedade é como a imagem de Stream Analytics é configurada com os detalhes específicos do seu trabalho.

   Por padrão, o módulo Stream Analytics usa o mesmo nome que o trabalho no qual ele se baseia. Você pode alterar o nome do módulo nesta página, se desejar, mas isso não é necessário.

1. Selecione **Cancelar** ou **salvar**.

1. Anote o nome do seu módulo de Stream Analytics porque você precisará dele na próxima etapa e, em seguida, selecione **Avançar: rotas** para continuar.

1. Na guia **rotas** , você define como as mensagens são passadas entre os módulos e o Hub IOT. As mensagens são construídas usando pares de nome/valor. Substitua o `route` padrão e o nome e os valores de `upstream` pelos pares mostrados na tabela a seguir, os seguintes pares de nome/valor, substituindo as instâncias de _{ModuleName}_ pelo nome do seu módulo de Azure Stream Analytics.

    | Nome | Valor |
    | --- | --- |
    | `telemetryToCloud` | `FROM /messages/modules/SimulatedTemperatureSensor/* INTO $upstream` |
    | `alertsToCloud` | `FROM /messages/modules/{moduleName}/* INTO $upstream` |
    | `alertsToReset` | `FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/SimulatedTemperatureSensor/inputs/control\")` |
    | `telemetryToAsa` | `FROM /messages/modules/SimulatedTemperatureSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")`|

    As rotas que declarar aqui definem o fluxo dos dados através do dispositivo IoT Edge. Os dados de telemetria do SimulatedTemperatureSensor são enviados ao Hub IoT e à entrada de **temperatura** que foi configurada no trabalho de Stream Analytics. As mensagens de saída de **alerta** são enviadas ao Hub IOT e ao módulo SimulatedTemperatureSensor para disparar o comando Reset.

1. Selecione **Avançar: revisar + criar**.

1. Na guia **revisar + criar** , você pode ver como as informações fornecidas no assistente são convertidas em um manifesto de implantação JSON. Quando terminar de revisar o manifesto, selecione **criar**.

1. Você será retornado para a página de detalhes do dispositivo. Selecione **Atualizar**.  

    Você deve ver o novo módulo Stream Analytics em execução, juntamente com os módulos IoT Edge Agent e IoT Edge Hub. Pode levar alguns minutos para que as informações cheguem ao dispositivo IoT Edge e, em seguida, para que os novos módulos sejam iniciados. Se você não vir os módulos que estão sendo executados imediatamente, continue a atualizar a página.

    ![Módulo SimulatedTemperatureSensor e ASA relatados pelo dispositivo](./media/tutorial-deploy-stream-analytics/module-output2.png)

## <a name="view-data"></a>Ver dados

Agora você pode acessar o dispositivo IoT Edge para conferir a interação entre o módulo Azure Stream Analytics e o módulo SimulatedTemperatureSensor.

1. Verifique se todos os módulos estão em execução no Docker:

   ```cmd/sh
   iotedge list  
   ```

1. Veja todos os dados de métricas e registos de sistema. Utilize o nome do módulo do Stream Analytics:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

1. Exibir o comando de redefinição afeta o SimulatedTemperatureSensor exibindo os logs de sensor:

   ```cmd/sh
   iotedge logs SimulatedTemperatureSensor
   ```

   Você pode observar a temperatura do computador gradualmente aumentar até atingir 70 graus por 30 segundos. Em seguida, o módulo do Stream Analytics aciona uma reposição e a temperatura da máquina baixa novamente para 21.

   ![Repor a saída do comando sobre os registos do módulo](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, você pode excluir as configurações locais e os recursos do Azure que você usou neste artigo para evitar cobranças.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurou uma tarefa do Azure Stream Analytics para analisar dados a partir do dispositivo IoT Edge. Em seguida, carregou este módulo do Azure Stream Analytics no dispositivo do IoT Edge para processar e reagir ao aumento da temperatura localmente, bem como enviar o fluxo de dados agregados para a cloud. Para ver de que forma o Azure IoT Edge pode criar mais soluções para a sua empresa, avance para os outros tutoriais.

> [!div class="nextstepaction"]
> [Implementar um modelo do Azure Machine Learning como um módulo](tutorial-deploy-machine-learning.md)
