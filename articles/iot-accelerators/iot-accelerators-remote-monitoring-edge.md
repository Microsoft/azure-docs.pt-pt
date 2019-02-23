---
title: Detetar anomalias na periferia um tutorial de solução – Azure | Documentos da Microsoft
description: Neste tutorial irá aprender a monitorizar os seus dispositivos IoT Edge com o acelerador de solução de monitorização remota.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: d28a88efc1a9f980d74737936bb960ba13573fa3
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56675097"
---
# <a name="tutorial-detect-anomalies-at-the-edge-with-the-remote-monitoring-solution-accelerator"></a>Tutorial: Detetar anomalias na periferia com o acelerador de solução de monitorização remota

Neste tutorial, vai configurar a solução de monitorização remota para responder a anomalias detetadas por um dispositivo IoT Edge. Dispositivos IoT Edge permitem-lhe processar a telemetria na periferia para reduzir o volume de telemetria enviada para a solução e ativar as respostas mais rápidas para eventos em dispositivos. Para saber mais sobre os benefícios do processamento do edge, veja [o que é o Azure IoT Edge](../iot-edge/about-iot-edge.md).

Para apresentar o limite de processamento com a monitorização remota, este tutorial utiliza um dispositivo de jack de bombeamento de petróleo simulado. Este jack de bombeamento de petróleo é gerida por uma organização chamada Contoso e está ligado para o acelerador de solução de monitorização remota. Sensores na jack de bomba petróleo medem a temperatura e a pressão. Operadores da Contoso sabem que um aumento anormal da temperatura pode causar o jack de bombeamento de petróleo abrandamento. Operadores da Contoso não precisam de monitorizar a temperatura do dispositivo quando está dentro do respetivo intervalo normal.

Contoso quer implementar um módulo de um edge inteligente para o jack de bombeamento de petróleo Deteta anomalias de temperatura. Outro módulo edge envia alertas para a solução de monitorização remota. Quando é recebido um alerta, um operador de Contoso pode expedir um técnico de manutenção. Contoso também pode configurar uma ação automática, tal como enviar uma mensagem de e-mail, para ser executada quando a solução recebe um alerta.

O diagrama seguinte mostra os principais componentes no cenário de introdução:

![Descrição geral](media/iot-accelerators-remote-monitoring-edge/overview.png)

Neste tutorial:

>[!div class="checklist"]
> * Adicionar um dispositivo IoT Edge para a solução
> * Criar um manifesto do Edge
> * Importar o manifesto como um pacote que define os módulos para executar no dispositivo
> * Implementar o pacote no seu dispositivo IoT Edge
> * Ver alertas do dispositivo

No dispositivo IoT Edge:

* O tempo de execução recebe o pacote e instala os módulos.
* O módulo do stream analytics Deteta anomalias de temperatura a bomba e envia comandos de resolver o problema.
* O módulo de análise de fluxo reencaminha dados filtrados para o solution accelerator.

Este tutorial utiliza uma máquina virtual Linux como um dispositivo IoT Edge. Também instalar um módulo de borda para simular o dispositivo de jack de bombeamento de petróleo.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-an-iot-edge-device"></a>Adicionar um dispositivo IoT Edge

Há duas etapas para adicionar um dispositivo IoT Edge para seu acelerador de solução de monitorização remota. Esta secção mostra-lhe como utilizar:

* Adicionar um dispositivo IoT Edge no **Device Explorer** página na monitorização remota da IU da web.
* Instale o runtime do IoT Edge numa máquina virtual (VM) do Linux.

### <a name="add-an-iot-edge-device-to-your-solution"></a>Adicionar um dispositivo IoT Edge à sua solução

Para adicionar um dispositivo IoT Edge para o acelerador de solução de monitorização remota, navegue para o **Device Explorer** página na IU da web e clique em **+ novo dispositivo**.

Na **novo dispositivo** painel, escolha **dispositivo IoT Edge** e introduza **bomba de petróleo** como o ID de dispositivo. Pode deixar os valores predefinidos para as outras definições. Em seguida, clique em **Aplicar**:

[![Adicionar dispositivo IoT Edge](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-expanded.png#lightbox)

Anote a cadeia de ligação do dispositivo, terá na secção seguinte deste tutorial.

Quando registar um dispositivo com o hub IoT no acelerador de solução de monitorização remota, é apresentada no **Device Explorer** página na IU da web:

[![Novo dispositivo IoT Edge](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-expanded.png#lightbox)

Para tornar mais fácil de gerir os dispositivos do IoT Edge na solução, crie um grupo de dispositivos e adicionar o dispositivo do IoT Edge:

1. Selecione o **bomba de petróleo** dispositivo na lista de **Device Explorer** página e, em seguida, clique em **tarefas**.

1. Criar uma tarefa para adicionar o **IsEdge** marca o dispositivo com as seguintes definições:

    | Definição | Value |
    | ------- | ----- |
    | Tarefa     | Etiquetas  |
    | Nome da tarefa | AddEdgeTag |
    | Chave     | IsOilPump |
    | Value   | S     |
    | Type    | Texto  |

    [![Adicionar etiqueta](./media/iot-accelerators-remote-monitoring-edge/addtag-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addtag-expanded.png#lightbox)

1. Clique em **aplicam-se**, em seguida, **fechar**.

1. Sobre o **Device Explorer** página, clique em **gerir grupos de dispositivos**.

1. Clique em **criar novo grupo de dispositivos**. Crie um novo grupo de dispositivos com as seguintes definições:

    | Definição | Valor |
    | ------- | ----- |
    | Name    | OilPumps |
    | Campo   | Tags.IsOilPump |
    | Operador | = Igual a |
    | Value    | S |
    | Type     | Texto |

    [![Criar grupo de dispositivos](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-expanded.png#lightbox)

1. Clique em **Guardar**.

Dispositivo IoT Edge está agora no **OilPumps** grupo.

### <a name="install-the-edge-runtime"></a>Instalar o runtime do Edge

Um dispositivo do Edge requer o runtime do Edge para ser instalado. Neste tutorial, instale o runtime do Edge numa VM do Linux do Azure para testar o cenário. Os passos seguintes utilizam o Azure cloud shell na instalação e configurar a VM:

1. Para criar uma VM do Linux no Azure, execute os seguintes comandos. Pode utilizar uma localização perto de onde estiver:

    ```azurecli-interactive
    az group create \
      --name IoTEdgeDevices \
      --location eastus
    az vm create \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest \
      --admin-username azureuser \
      --generate-ssh-keys \
      --size Standard_B1ms
    ```

1. Para configurar o runtime do Edge com a cadeia de ligação do dispositivo, execute o comando seguinte com a cadeia de ligação do dispositivo que anotou anteriormente:

    ```azurecli-interactive
    az vm run-command invoke \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --command-id RunShellScript \
      --scripts 'sudo /etc/iotedge/configedge.sh "YOUR_DEVICE_CONNECTION_STRING"'
    ```

    Certifique-se de que incluem a cadeia de ligação dentro de aspas.

Agora instalado e configurado o runtime do IoT Edge num dispositivo Linux. Mais tarde neste tutorial, vai utilizar a solução de monitorização remota para implementar módulos do IoT Edge deste dispositivo.

## <a name="create-an-edge-manifest"></a>Criar um manifesto do Edge

Para simular o dispositivo de bombeamento de jack de petróleo, terá de adicionar os seguintes módulos para o seu dispositivo Edge:

* Módulo de simulação de temperatura.
* Deteção de anomalias do Azure Stream Analytics.

Os passos seguintes mostram como criar um manifesto de implantação do Edge que inclui estes módulos. Mais tarde neste tutorial importará esse manifesto como um pacote do solution Accelerator monitorização remota.

### <a name="create-the-azure-stream-analytics-job"></a>Criar tarefa do Azure Stream Analytics

Definir a tarefa do Stream Analytics no portal do antes de empacotá-lo como um módulo de borda.

1. No portal do Azure, criar uma conta de armazenamento do Azure com as opções predefinidas no **IoTEdgeDevices** grupo de recursos. Anote o nome que escolheu.

1. No portal do Azure, crie uma **tarefa do Stream Analytics** no **IoTEdgeDevices** grupo de recursos. Utilizar valores de configuração a seguir:

    | Opção | Value |
    | ------ | ----- |
    | Nome da tarefa | EdgeDeviceJob |
    | Subscrição | A sua subscrição do Azure |
    | Grupo de recursos | IoTEdgeDevices |
    | Localização | EUA Leste |
    | Ambiente de alojamento | Microsoft Edge |
    | Unidades de transmissão em fluxo | 1 |

1. Abra o **EdgeDeviceJob** Stream Analytics da tarefa no portal, clique em entradas e adicionar um **Hub do Edge** transmitir entrada chamada **telemetria**.

1. Na **EdgeDeviceJob** tarefa do Stream Analytics no portal, clique em **saídas** e adicione um **Hub do Edge** saída chamada **saída**.

1. Na **EdgeDeviceJob** tarefa do Stream Analytics no portal, clique em **saídas** e adicione um segundo **Hub do Edge** saída chamada **alerta**.

1. Na **EdgeDeviceJob** tarefa do Stream Analytics no portal, clique em **consulta** e adicione as seguintes **selecionar** instrução:

    ```sql
    SELECT  
    avg(machine.temperature) as temperature, 
    'F' as temperatureUnit 
    INTO output 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 5) 
    SELECT 'reset' as command 
    INTO alert 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 3) 
    HAVING avg(machine.temperature) > 400
    ```

1. Na **EdgeDeviceJob** tarefa do Stream Analytics no portal, clique em **definições de conta de armazenamento**. Adicione a conta de armazenamento adicionado para o **IoTEdgeDevices** grupo de recursos como o início desta secção. Criar um novo recipiente chamado **edgeconfig**.

Captura de ecrã seguinte mostra a tarefa de Stream Analytics guardada:

[![Tarefa do Stream Analytics](./media/iot-accelerators-remote-monitoring-edge/streamjob-inline.png)](./media/iot-accelerators-remote-monitoring-edge/streamjob-expanded.png#lightbox)

Agora que definiu uma tarefa de Stream Analytics para executar no seu dispositivo do edge. A tarefa calcula a temperatura média numa janela de 5 segundos. A tarefa também envia um alerta caso a temperatura média numa janela de 3 segundos mais 400.

### <a name="create-the-iot-edge-deployment"></a>Criar a implementação de IoT Edge

Em seguida, vai criar um manifesto de implantação do IoT Edge que define os módulos para ser executado no seu dispositivo Edge. Na secção seguinte, é possível importar este manifesto como um pacote da solução de monitorização remota.

1. No portal do Azure, navegue para o hub IoT na sua solução de monitorização remota. Pode encontrar o hub IoT no grupo de recursos que tem o mesmo nome que a sua solução de monitorização remota.

1. No IoT hub, clique em **IoT Edge** no **gestão de dispositivos automático** secção. Clique em **adicionar uma implementação de IoT Edge**.

1. Sobre o **criar implementação > nome e a etiqueta** página, introduza o nome **petróleo-bomba-device**. Clique em **Seguinte**.

1. Sobre o **criar implementação > Adicionar módulos** página, clique em **+ adicionar**. Escolher **módulo do IoT Edge**.

1. Na **módulos do IoT Edge personalizada** painel, introduza **sensor de temperatura** como o nome, e **asaedgedockerhubtest/asa-edge-test-module:sensor-ad-linux-amd64** como o URI da imagem. Clique em **Guardar**.

1. Sobre o **criar implementação > Adicionar módulos** página, clique em **+ adicionar** para adicionar um módulo de segundo. Escolher **módulo do Azure Stream Analytics**.

1. Na **implementação de borda** painel, selecione a sua subscrição e o **EdgeDeviceJob** que criou na secção anterior. Clique em **Guardar**.

1. Sobre o **criar implementação > Adicionar módulos** página, clique em **próxima**.

1. Sobre o **criar implementação > especificar rotas** página, adicione o seguinte código:

    ```sql
    {
      "routes": { 
        "alertsToReset": "FROM /messages/modules/EdgeDeviceJob/outputs/alert INTO BrokeredEndpoint(\"/modules/temperatureSensor/inputs/control\")", 
        "telemetryToAsa": "FROM /messages/modules/temperatureSensor/* INTO BrokeredEndpoint(\"/modules/EdgeDeviceJob/inputs/telemetry\")", 
        "ASAToCloud": "FROM /messages/modules/EdgeDeviceJob/* INTO $upstream" 
      } 
    }
    ```

    Esse código encaminha a saída do módulo do Stream Analytics para locais corretos.

    Clique em **Seguinte**.

1. Sobre o **criar implementação > especificar métricas** página, clique em **seguinte**.

1. Sobre o **criar implementação > dispositivos de destino** página, introduza 10 como a prioridade. Clique em **Seguinte**.

1. Na **criar implementação > implementação de revisão** página, clique em **submeter**:

    [![Implementação de revisão](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-expanded.png#lightbox)

1. No principal **IoT Edge** página, clique em **implementações do IoT Edge**. Pode ver **petróleo-bomba-device** na lista de implementações.

1. Clique nas **petróleo-bomba-device** implementação e clique em **manifesto de transferir o IoT Edge**. Guarde o ficheiro como **petróleo-bomba-device.json** para um local adequado no seu computador local. Precisa de ter este ficheiro na secção seguinte deste tutorial.

Acabou de criar um manifesto do IoT Edge para importar para a solução de monitorização remota como um pacote. Normalmente, um desenvolvedor cria o IoT Edge, módulos e o arquivo de manifesto.

## <a name="import-a-package"></a>Importar um pacote

Nesta secção, é possível importar o manifesto do Edge como um pacote da solução de monitorização remota.

1. Na monitorização remota da IU da web, navegue para o **pacotes** página e clique em **+ novo pacote**:

    [![Novo pacote](./media/iot-accelerators-remote-monitoring-edge/newpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newpackage-expanded.png#lightbox)

1. No **novo pacote** painel, escolha **Edge manifesto** como o tipo de pacote, clique em **procurar** para encontrar o **petróleo-bomba-device.json** de ficheiros no seu computador local e clique em **carregar**:

    [![Carregar o pacote](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-expanded.png#lightbox)

    A lista de pacotes agora inclui a **petróleo-bomba-device.json** pacote.

Na secção seguinte, cria uma implementação que aplica-se o pacote para o seu dispositivo do Edge.

## <a name="deploy-a-package"></a>Implementar um pacote

Agora está pronto para implementar o pacote para o seu dispositivo.

1. Na monitorização remota da IU da web, navegue para o **implementações** página e clique em **+ nova implementação**:

    [![Nova implementação](./media/iot-accelerators-remote-monitoring-edge/newdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newdeployment-expanded.png#lightbox)

1. Na **nova implementação** painel, pode criar uma implementação com as seguintes definições:

    | Opção | Value |
    | ------ | ----- |
    | Name   | OilPumpDevices |
    | Tipo de pacote | Manifesto do Edge |
    | Pacote | oil-pump-device.json |
    | Grupo de dispositivos | OilPumps |
    | Prioridade | 10 |

    [![Criar implementação](./media/iot-accelerators-remote-monitoring-edge/createdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdeployment-expanded.png#lightbox)

    Clique em **Aplicar**.

Terá de aguardar alguns minutos para o pacote para implementar no seu dispositivo e para a telemetria para começarem a fluir do dispositivo.

[![Implementação do Active Directory](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-inline.png)](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-expanded.png#lightbox)

O **implementações** página mostra as métricas seguintes:

* **Direcionada** mostra o número de dispositivos no grupo de dispositivos.
* **Aplicado** mostra o número de dispositivos que tenha tido a conteúdo de implementação aplicada.
* **Foi efetuada com êxito** mostra o número de dispositivos de ponta na implementação do relatório de sucesso do tempo de execução de cliente do IoT Edge.
* **Falha ao** mostra o número de dispositivos de ponta na implementação de relatórios de falha de tempo de execução do cliente do IoT Edge.

## <a name="monitor-the-device"></a>Monitorizar o dispositivo

Pode ver a telemetria de temperatura do seu dispositivo de bombeamento de petróleo em monitorização remota da IU da web:

1. Navegue para o **Device Explorer** página e selecione o seu dispositivo de bombeamento de petróleo.
1. Na **telemetria** secção a **detalhes do dispositivo** painel, clique em **temperatura**:

    [![Ver a telemetria](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-expanded.png#lightbox)

Pode ver como a temperatura sobe até atingir um limiar. O módulo Edge do Stream Analytics Deteta quando a temperatura atinge este limite e envia um comando ao dispositivo para reduzir a temperatura imediatamente. Todo esse processamento ocorre no dispositivo sem comunicar com a cloud.

Se pretender notificar operadores quando foi atingido o limiar, pode criar uma regra de monitorização remota da IU da web:

1. Navegue para o **regras** página e clique em **+ nova regra**.
1. Crie uma nova regra com as seguintes definições:

    | Opção | Value |
    | ------ | ----- |
    | Nome da regra | Temperatura de bombeamento de petróleo |
    | Descrição | Temperatura de bombeamento de petróleo excedido 300 |
    | Grupo de dispositivos | OilPumps |
    | Cálculo | Instantâneo |
    | Campo | temperatura |
    | Operador | > |
    | Value | 300 |
    | Nível de gravidade | Informações |

    [![Criar regra](./media/iot-accelerators-remote-monitoring-edge/newrule-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newrule-expanded.png#lightbox)

    Clique em **Aplicar**.

1. Navegue para o **Dashboard** página. Um alerta mostra o **alertas** painel quando a temperatura no **bomba de petróleo** dispositivo for superior a 300.

## <a name="next-steps"></a>Passos Seguintes

Este tutorial mostrou como adicionar e configurar um dispositivo IoT Edge no acelerador de solução de monitorização remota. Para saber mais sobre como trabalhar com pacotes do IoT Edge da solução de monitorização remota, consulte o guia de procedimentos seguinte:

> [!div class="nextstepaction"]
> [Importar um pacote do IoT Edge para o seu acelerador de solução de monitorização remota](iot-accelerators-remote-monitoring-import-edge-package.md)

Para saber mais sobre como instalar o runtime do IoT Edge, veja [instalar o runtime do Azure IoT Edge no Linux (x64)](../iot-edge/how-to-install-iot-edge-linux.md).

Para saber mais sobre o Azure Stream Analytics em dispositivos periféricos, veja [implementar o Azure Stream Analytics como um módulo do IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md).
