---
title: Detetar anomalias na borda num tutorial de solução - Azure / Microsoft Docs
description: Neste tutorial aprende-se a monitorizar os seus dispositivos IoT Edge utilizando o acelerador de soluções de monitorização remota.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: a812155474b244682613b38b9b9379fa6cdcdcd8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "66117606"
---
# <a name="tutorial-detect-anomalies-at-the-edge-with-the-remote-monitoring-solution-accelerator"></a>Tutorial: Detetar anomalias na borda com o acelerador de solução de monitorização remota

Neste tutorial, configura a solução de Monitorização Remota para responder às anomalias detetadas por um dispositivo IoT Edge. Os dispositivos IoT Edge permitem processar a telemetria na borda para reduzir o volume de telemetria enviado para a solução e permitir respostas mais rápidas aos eventos nos dispositivos. Para saber mais sobre os benefícios do processamento de bordas, consulte [o que é Azure IoT Edge](../iot-edge/about-iot-edge.md).

Para introduzir o processamento de bordas com monitorização remota, este tutorial utiliza um dispositivo simulado de tomada de bomba de óleo. Este macaco da bomba de óleo é gerido por uma organização chamada Contoso e está ligado ao acelerador de solução de monitorização remota. Os sensores na tomada da bomba de óleo medem a temperatura e a pressão. Os operadores da Contoso sabem que um aumento anormal da temperatura pode fazer com que o macaco da bomba de óleo abrande. Os operadores da Contoso não precisam de monitorizar a temperatura do dispositivo quando está dentro do seu alcance normal.

Contoso quer implantar um módulo de borda inteligente para a tomada da bomba de óleo que deteta anomalias de temperatura. Outro módulo de borda envia alertas para a solução de Monitorização Remota. Quando um alerta é recebido, um operador de Contoso pode enviar um técnico de manutenção. Contoso também poderia configurar uma ação automatizada, como o envio de um e-mail, para executar quando a solução recebe um alerta.

O diagrama a seguir mostra os componentes-chave no cenário tutorial:

![Descrição geral](media/iot-accelerators-remote-monitoring-edge/overview.png)

Neste tutorial:

>[!div class="checklist"]
> * Adicione um dispositivo IoT Edge à solução
> * Criar um manifesto Edge
> * Importar o manifesto como um pacote que define os módulos para executar no dispositivo
> * Desloque o pacote para o seu dispositivo IoT Edge
> * Ver alertas do dispositivo

No dispositivo IoT Edge:

* O tempo de funcionação recebe a embalagem e instala os módulos.
* O módulo de análise de fluxo deteta anomalias de temperatura na bomba e envia comandos para resolver o problema.
* O módulo de análise de fluxo encaminha os dados filtrados para o acelerador da solução.

Este tutorial usa uma máquina virtual Linux como um dispositivo IoT Edge. Também instala um módulo de borda para simular o dispositivo de tomada da bomba de óleo.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-an-iot-edge-device"></a>Adicionar um dispositivo IoT Edge

Existem dois passos para adicionar um dispositivo IoT Edge ao seu acelerador de solução de monitorização remota. Esta secção mostra-lhe como usar:

* Adicione um dispositivo IoT Edge na página **do Device Explorer** na UI web de monitorização remota.
* Instale o tempo de execução do IoT Edge numa máquina virtual Linux (VM).

### <a name="add-an-iot-edge-device-to-your-solution"></a>Adicione um dispositivo IoT Edge à sua solução

Para adicionar um dispositivo IoT Edge ao acelerador de solução de monitorização remota, navegue na página do **Device Explorer** na Web UI e clique + **novo dispositivo**.

No painel **do novo dispositivo,** escolha o **dispositivo IoT Edge** e **introduza** a bomba de óleo como id do dispositivo. Pode deixar os valores predefinidos para as outras definições. Em seguida, clique em **Aplicar**:

[![Adicione o dispositivo IoT Edge](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-expanded.png#lightbox)

Tome nota da cadeia de ligação do dispositivo, precisa dela na secção seguinte deste tutorial.

Ao registar um dispositivo com o hub IoT no acelerador de soluções de monitorização remota, está listado na página do **Device Explorer** na UI web:

[![Novo dispositivo IoT Edge](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-expanded.png#lightbox)

Para facilitar a gestão dos dispositivos IoT Edge na solução, crie um grupo de dispositivos e adicione o dispositivo IoT Edge:

1. Selecione o dispositivo **de bomba de óleo** na lista na página do Device **Explorer** e, em seguida, clique em **Jobs**.

1. Criar um trabalho para adicionar a etiqueta **IsEdge** ao dispositivo utilizando as seguintes definições:

    | Definição | Valor |
    | ------- | ----- |
    | Tarefa     | Etiquetas  |
    | Nome da Tarefa | AddedgeTag |
    | Chave     | IsOilPump |
    | Valor   | S     |
    | Tipo    | Texto  |

    [![Adicionar etiqueta](./media/iot-accelerators-remote-monitoring-edge/addtag-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addtag-expanded.png#lightbox)

1. Clique **em Aplicar** **e,** em seguida, fechar .

1. Na página do Explorer do **Dispositivo,** clique em **Gerir grupos de dispositivos**.

1. Clique **em Criar um novo grupo de dispositivos**. Criar um novo grupo de dispositivos com as seguintes definições:

    | Definição | Valor |
    | ------- | ----- |
    | Nome    | Bombas de Óleo |
    | Campo   | Tags.IsOilPump |
    | Operador | = Iguais |
    | Valor    | S |
    | Tipo     | Texto |

    [![Criar grupo de dispositivos](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-expanded.png#lightbox)

1. Clique em **Guardar**.

O dispositivo IoT Edge está agora no grupo **OilPumps.**

### <a name="install-the-edge-runtime"></a>Instale o tempo de execução edge

Um dispositivo Edge requer a instalação do tempo de execução edge. Neste tutorial, instala o tempo de execução edge num VM Azure Linux para testar o cenário. Os seguintes passos utilizam a casca de nuvem Azure na instalação e configuram o VM:

1. Para criar um VM Linux em Azure, execute os seguintes comandos. Pode utilizar um local próximo do local onde se encontram:

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

1. Para configurar o tempo de execução do Edge com a cadeia de ligação do dispositivo, execute o seguinte comando utilizando a cadeia de ligação do dispositivo que tomou uma nota anteriormente:

    ```azurecli-interactive
    az vm run-command invoke \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --command-id RunShellScript \
      --scripts 'sudo /etc/iotedge/configedge.sh "YOUR_DEVICE_CONNECTION_STRING"'
    ```

    Certifique-se de incluir a sua corda de ligação dentro de marcas de dupla aspas.

Já instalou e configurou o tempo de execução do IoT Edge num dispositivo Linux. Mais tarde neste tutorial, utiliza a solução de Monitorização Remota para implantar módulos IoT Edge neste dispositivo.

## <a name="create-an-edge-manifest"></a>Criar um manifesto Edge

Para simular o dispositivo de bomba de tomada de óleo, é necessário adicionar os seguintes módulos ao seu dispositivo Edge:

* Módulo de simulação de temperatura.
* Deteção de anomalias azure Stream Analytics.

Os seguintes passos mostram-lhe como criar um manifesto de implantação edge que inclui estes módulos. Mais tarde neste tutorial importa este manifesto como um pacote no acelerador de solução de monitorização remota.

### <a name="create-the-azure-stream-analytics-job"></a>Criar o trabalho de Analytics Azure Stream

Define o trabalho stream analytics no portal antes de o embalar como módulo Edge.

1. No portal Azure, crie uma conta de armazenamento Azure utilizando as opções predefinidas no grupo de recursos **IoTEdgeDevices.** Tome nota do nome que escolheu.

1. No portal Azure, crie um **Trabalho stream analytics** no grupo de recursos **IoTEdgeDevices.** Utilize os seguintes valores de configuração:

    | Opção | Valor |
    | ------ | ----- |
    | Nome da tarefa | EdgeDeviceJob |
    | Subscrição | A sua subscrição do Azure |
    | Grupo de recursos | Dispositivos IoTEdge |
    | Localização | E.U.A. Leste |
    | Ambiente de alojamento | Microsoft Edge |
    | Unidades de transmissão em fluxo | 1 |

1. Abra o trabalho **edgeDeviceJob** Stream Analytics no portal, clique em Inputs e adicione uma entrada de fluxo **Edge Hub** chamada **telemetria**.

1. No trabalho **edgeDeviceJob** Stream Analytics no portal, clique em **Saídas** e adicione uma saída **edge hub** chamada **saída**.

1. No trabalho **edgeDeviceJob** Stream Analytics no portal, clique em **Saídas** e adicione uma segunda saída **edge hub** chamada **alerta**.

1. No trabalho **edgeDeviceJob** Stream Analytics no portal, clique em **Consulta** e adicione a seguinte declaração **selecionada:**

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

1. No trabalho **edgeDeviceJob** Stream Analytics no portal, clique em **definições**de conta de armazenamento . Adicione a conta de armazenamento adicionada ao grupo de recursos **IoTEdgeDevices** como o início desta secção. Crie um novo recipiente chamado **edgeconfig.**

A imagem que se segue mostra o trabalho guardado de Stream Analytics:

[![Trabalho de Análise de Fluxo](./media/iot-accelerators-remote-monitoring-edge/streamjob-inline.png)](./media/iot-accelerators-remote-monitoring-edge/streamjob-expanded.png#lightbox)

Definiu agora um trabalho de Stream Analytics para executar no seu dispositivo de borda. O trabalho calcula a temperatura média sobre uma janela de 5 segundos. O trabalho também envia um alerta se a temperatura média numa janela de 3 segundos for superior a 400.

### <a name="create-the-iot-edge-deployment"></a>Criar a implantação ioT Edge

Em seguida, cria um manifesto de implantação IoT Edge que define os módulos para executar no seu dispositivo Edge. Na secção seguinte, importa este manifesto como um pacote na solução de Monitorização Remota.

1. No portal Azure, navegue até ao centro IoT na sua solução de Monitorização Remota. Pode encontrar o hub IoT no grupo de recursos que tem o mesmo nome que a sua solução de Monitorização Remota.

1. No centro IoT, clique em **IoT Edge** na secção De **Gestão automática de Dispositivos.** Clique **em Adicionar uma implementação IoT Edge**.

1. Na página **Create Implantação > Nome e Etiqueta,** introduza o nome de **dispositivo de bomba de óleo**. Clique em **Seguinte**.

1. Na página **Criar > adicionar módulos,** clique **+ adicionar**. Escolha módulo **IoT Edge**.

1. No painel **Módulos Personalizados IoT Edge,** introduza a **temperaturaSensor** como o nome, e **asaedgedockerhubtest/asa-edge-test-module:sensor-ad-linux-amd64** como a imagem URI. Clique em **Guardar**.

1. Na página **Create Deployment > Adicionar Módulos,** clique + **adicione** para adicionar um segundo módulo. Escolha **o Módulo de Análise de Fluxo Azure**.

1. No painel de **implementação Edge,** selecione a sua subscrição e o **EdgeDeviceJob** que criou na secção anterior. Clique em **Guardar**.

1. Na página **Criar > adicionar módulos,** clique em **Seguinte**.

1. Na página **Create Deployment > Especificar Rotas,** adicione o seguinte código:

    ```sql
    {
      "routes": { 
        "alertsToReset": "FROM /messages/modules/EdgeDeviceJob/outputs/alert INTO BrokeredEndpoint(\"/modules/temperatureSensor/inputs/control\")", 
        "telemetryToAsa": "FROM /messages/modules/temperatureSensor/* INTO BrokeredEndpoint(\"/modules/EdgeDeviceJob/inputs/telemetry\")", 
        "ASAToCloud": "FROM /messages/modules/EdgeDeviceJob/* INTO $upstream" 
      } 
    }
    ```

    Este código direciona a saída do módulo Stream Analytics para os locais corretos.

    Clique em **Seguinte**.

1. Na página **Criar > especificar Métricas,** clique em **Seguinte**.

1. Na página **Create Deployment > Target Devices,** insira 10 como prioridade. Clique em **Seguinte**.

1. Na página **de implementação de implementação de > de implementação** de implementação de implementação de implementação de implementação, clique em **Submeter:**

    [![Revisão da implantação](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-expanded.png#lightbox)

1. Na página **ioT edge** principal, clique em **implementações IoT Edge**. Pode ver o **dispositivo de bomba de óleo** na lista de implantações.

1. Clique na implementação do **dispositivo de bomba de óleo** e, em seguida, clique em descarregar o manifesto **Descarregamento IoT Edge**. Guarde o ficheiro como **dispositivo de bomba de óleo.json** para um local adequado na sua máquina local. Precisa deste ficheiro na próxima secção deste tutorial.

Criou agora um manifesto IoT Edge para importar para a solução de Monitorização Remota como pacote. Tipicamente, um desenvolvedor cria os módulos IoT Edge e o ficheiro manifesto.

## <a name="import-a-package"></a>Importar um pacote

Nesta secção, importa o manifesto Edge como um pacote na solução de Monitorização Remota.

1. Na UI web de monitorização remota, navegue para a página **Pacotes** e clique **em + Novo Pacote:**

    [![Novo pacote](./media/iot-accelerators-remote-monitoring-edge/newpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newpackage-expanded.png#lightbox)

1. No painel **New Package,** escolha **Edge Manifest** como o tipo de pacote, clique em **Navegar** para encontrar o ficheiro **oil-pump-device.json** na sua máquina local e clique em **Carregar:**

    [![Pacote de upload](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-expanded.png#lightbox)

    A lista de pacotes inclui agora o pacote **oil-pump-device.json.**

Na secção seguinte, cria-se uma implementação que aplica o pacote ao seu dispositivo Edge.

## <a name="deploy-a-package"></a>Implementar um pacote

Agora está pronto para enviar o pacote para o seu dispositivo.

1. Na UI web de monitorização remota, navegue para a página **de Implementações** e clique **+ Nova implementação:**

    [![Nova implantação](./media/iot-accelerators-remote-monitoring-edge/newdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newdeployment-expanded.png#lightbox)

1. No novo painel de **implantação,** crie uma implementação com as seguintes definições:

    | Opção | Valor |
    | ------ | ----- |
    | Nome   | Dispositivos de Bombas de Óleo |
    | Tipo de pacote | Manifesto de borda |
    | Pacote | oil-pump-device.json |
    | Grupo de Dispositivos | Bombas de Óleo |
    | Prioridade | 10 |

    [![Criar implementação](./media/iot-accelerators-remote-monitoring-edge/createdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdeployment-expanded.png#lightbox)

    Clique em **Aplicar**.

Tem de esperar vários minutos para que a embalagem seja implantada no seu dispositivo e para que a telemetria comece a fluir a partir do dispositivo.

[![Implantação ativa](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-inline.png)](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-expanded.png#lightbox)

A página **de Implementações** mostra as seguintes métricas:

* **O alvo** mostra o número de dispositivos no grupo do dispositivo.
* **A aplicação** mostra o número de dispositivos que tiveram o conteúdo de implantação aplicado.
* **O sucesso** mostra o número de dispositivos Edge no sucesso de relatórios de implementação do tempo de execução do cliente IoT Edge.
* **Falha mostra** o número de dispositivos Edge na falha de reporte de implementação do tempo de execução do cliente IoT Edge.

## <a name="monitor-the-device"></a>Monitorize o dispositivo

Pode visualizar a telemetria de temperatura do seu dispositivo de bomba de óleo na UI web de monitorização remota:

1. Navegue na página **Do Explorador** do Dispositivo e selecione o seu dispositivo de bomba de óleo.
1. Na secção **telemetria** do painel de detalhes do **Dispositivo,** clique em **Temperatura:**

    [![Ver telemetria](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-expanded.png#lightbox)

Pode ver como a temperatura sobe até atingir um limiar. O módulo Stream Analytics Edge deteta quando a temperatura atinge este limiar e envia um comando para o dispositivo para reduzir imediatamente a temperatura. Todo este processamento acontece no dispositivo sem comunicar com a nuvem.

Se pretender notificar os operadores quando o limiar foi atingido, pode criar uma regra na UI web de monitorização remota:

1. Navegue para a página **Regras** e clique **+ Nova regra**.
1. Criar uma nova regra com as seguintes definições:

    | Opção | Valor |
    | ------ | ----- |
    | Nome da regra | Temperatura da bomba de óleo |
    | Descrição | Temperatura da bomba de óleo ultrapassou os 300 |
    | Grupo de dispositivos | Bombas de Óleo |
    | Cálculo | Instantâneo |
    | Campo | temperatura |
    | Operador | > |
    | Valor | 300 |
    | Nível de gravidade | Informações |

    [![Criar regra](./media/iot-accelerators-remote-monitoring-edge/newrule-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newrule-expanded.png#lightbox)

    Clique em **Aplicar**.

1. Navegue para a página **do Dashboard.** Um alerta mostra no painel **alertas** quando a temperatura no dispositivo **de bomba de óleo** ultrapassa os 300.

## <a name="next-steps"></a>Passos seguintes

Este tutorial mostrou-lhe como adicionar e configurar um dispositivo IoT Edge no acelerador de solução de monitorização remota. Para saber mais sobre o trabalho com os pacotes IoT Edge na solução de Monitorização Remota, consulte o seguinte guia:

> [!div class="nextstepaction"]
> [Importe um pacote IoT Edge para o seu acelerador de solução de monitorização remota](iot-accelerators-remote-monitoring-import-edge-package.md)

Para saber mais sobre a instalação do tempo de execução do IoT Edge, consulte Instale o tempo de execução do [Edge Azure IoT no Linux (x64)](../iot-edge/how-to-install-iot-edge-linux.md).

Para saber mais sobre o Azure Stream Analytics em dispositivos Edge, consulte [o Deploy Azure Stream Analytics como um módulo IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md).
