---
title: Detetar anomalias na borda numa solução tutorial - Azure Microsoft Docs
description: Neste tutorial aprende-se a monitorizar os seus dispositivos IoT Edge utilizando o acelerador de solução de monitorização remota.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 43ba14845765230b9a54c2b34dbc7ccd53af950b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90970004"
---
# <a name="tutorial-detect-anomalies-at-the-edge-with-the-remote-monitoring-solution-accelerator"></a>Tutorial: Detetar anomalias na borda com o acelerador de solução de monitorização remota

Neste tutorial, configura a solução de Monitorização Remota para responder às anomalias detetadas por um dispositivo IoT Edge. Os dispositivos IoT Edge permitem processar a telemetria na borda para reduzir o volume de telemetria enviada para a solução e para permitir respostas mais rápidas aos eventos nos dispositivos. Para saber mais sobre os benefícios do processamento de bordas, consulte [O que é Azure IoT Edge](../iot-edge/about-iot-edge.md).

Para introduzir o processamento de borda com monitorização remota, este tutorial utiliza um dispositivo simulado de tomada de óleo. Esta tomada de bomba de óleo é gerida por uma organização chamada Contoso e está ligada ao acelerador de solução de monitorização remota. Os sensores na tomada da bomba de óleo medem a temperatura e a pressão. Os operadores da Contoso sabem que um aumento anormal da temperatura pode fazer com que a bomba de óleo abrande. Os operadores da Contoso não precisam de monitorizar a temperatura do dispositivo quando está dentro do seu alcance normal.

O Contoso quer implantar um módulo de borda inteligente na bomba de óleo que deteta anomalias de temperatura. Outro módulo de borda envia alertas para a solução de Monitorização Remota. Quando um alerta é recebido, um operador da Contoso pode enviar um técnico de manutenção. A Contoso também poderia configurar uma ação automatizada, como o envio de um e-mail, para correr quando a solução receber um alerta.

O diagrama a seguir mostra os componentes-chave no cenário tutorial:

![O diagrama mostra a tomada de óleo ligada ao módulo de análise de fluxo I o T Edge no dispositivo I o T Edge para telemetria e comandos. A telemetria filtrada vai para o dispositivo I o T Edge no acelerador de solução de monitorização remota na nuvem. A nuvem também contém implantação e pacote. A implementação implementa o tempo de funcionamento I o T Edge no dispositivo.](media/iot-accelerators-remote-monitoring-edge/overview.png)

Neste tutorial:

>[!div class="checklist"]
> * Adicione um dispositivo IoT Edge à solução
> * Criar um manifesto Edge
> * Importe o manifesto como um pacote que define os módulos para executar no dispositivo
> * Desloque a embalagem para o seu dispositivo IoT Edge
> * Ver alertas do dispositivo

No dispositivo IoT Edge:

* O tempo de funcionação recebe o pacote e instala os módulos.
* O módulo de análise de fluxo deteta anomalias de temperatura na bomba e envia comandos para resolver o problema.
* O módulo de análise de fluxo encaminha dados filtrados para o acelerador de solução.

Este tutorial utiliza uma máquina virtual Linux como dispositivo IoT Edge. Também instala um módulo de borda para simular o dispositivo de tomada de óleo.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-an-iot-edge-device"></a>Adicionar um dispositivo IoT Edge

Existem dois passos para adicionar um dispositivo IoT Edge ao seu acelerador de solução de monitorização remota. Esta secção mostra-lhe como usar:

* Adicione um dispositivo IoT Edge na página **device Explorer** na UI web de monitorização remota.
* Instale o tempo de funcionação do IoT Edge numa máquina virtual Linux (VM).

### <a name="add-an-iot-edge-device-to-your-solution"></a>Adicione um dispositivo IoT Edge à sua solução

Para adicionar um dispositivo IoT Edge ao acelerador de solução de monitorização remota, navegue na página **Device Explorer** na UI web e clique em + **novo dispositivo**.

No painel do **novo dispositivo,** escolha o **dispositivo IoT Edge** e **introduza a bomba de óleo** como iD do dispositivo. Pode deixar os valores predefinidos para as outras definições. Em seguida, clique em **Aplicar**:

[![Adicionar dispositivo IoT Edge](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-expanded.png#lightbox)

Tome nota da cadeia de ligação do dispositivo, precisando na secção seguinte deste tutorial.

Quando regista um dispositivo com o hub IoT no acelerador de solução de monitorização remota, está listado na página do Explorador de **Dispositivos** na UI web:

[![Novo dispositivo IoT Edge](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-expanded.png#lightbox)

Para facilitar a gestão dos dispositivos IoT Edge na solução, crie um grupo de dispositivos e adicione o dispositivo IoT Edge:

1. Selecione o dispositivo **de bomba de óleo** na lista na página Device **Explorer** e, em seguida, clique em **Jobs**.

1. Crie um trabalho para adicionar a etiqueta **IsEdge** ao dispositivo utilizando as seguintes definições:

    | Definição | Valor |
    | ------- | ----- |
    | Tarefa     | Etiquetas  |
    | Nome da Tarefa | AddEdgeTag |
    | Chave     | IsOilPump |
    | Valor   | S     |
    | Tipo    | Texto  |

    [![Adicionar etiqueta](./media/iot-accelerators-remote-monitoring-edge/addtag-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addtag-expanded.png#lightbox)

1. Clique **em Aplicar**e, em seguida, **Fechar**.

1. Na página **Device Explorer,** clique em **Gerir grupos de dispositivos**.

1. Clique **em Criar novo grupo de dispositivos**. Criar um novo grupo de dispositivos com as seguintes definições:

    | Definição | Valor |
    | ------- | ----- |
    | Nome    | OilPumps |
    | Campo   | Tags.IsOilPump |
    | Operador | = Iguais |
    | Valor    | S |
    | Tipo     | Texto |

    [![Criar grupo de dispositivos](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-expanded.png#lightbox)

1. Clique em **Guardar**.

O dispositivo IoT Edge está agora no grupo **OilPumps.**

### <a name="install-the-edge-runtime"></a>Instale o tempo de execução edge

Um dispositivo Edge requer a instalação do tempo de execução edge. Neste tutorial, instale o tempo de execução edge num VM Azure Linux para testar o cenário. Os seguintes passos utilize a casca de nuvem Azure na instalação e configurar o VM:

1. Para criar um Linux VM em Azure, executar os seguintes comandos. Você pode usar um local perto de onde você está:

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

1. Para configurar o tempo de funcionamento da Borda com a cadeia de ligação do dispositivo, executar o seguinte comando utilizando a cadeia de ligação do dispositivo que fez uma nota anteriormente:

    ```azurecli-interactive
    az vm run-command invoke \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --command-id RunShellScript \
      --scripts 'sudo /etc/iotedge/configedge.sh "YOUR_DEVICE_CONNECTION_STRING"'
    ```

    Certifique-se de que inclui a sua cadeia de ligação dentro de marcas de aspas duplas.

Instalou e configuraram o tempo de funcionaamento do IoT Edge num dispositivo Linux. Mais tarde neste tutorial, utiliza a solução de Monitorização Remota para implantar módulos IoT Edge neste dispositivo.

## <a name="create-an-edge-manifest"></a>Criar um manifesto Edge

Para simular o dispositivo de bomba de tomada de óleo, é necessário adicionar os seguintes módulos ao seu dispositivo Edge:

* Módulo de simulação de temperatura.
* Deteção de anomalias Azure Stream Analytics.

Os passos seguintes mostram-lhe como criar um manifesto de implantação edge que inclui estes módulos. Mais tarde neste tutorial importa este manifesto como um pacote no acelerador de solução de monitorização remota.

### <a name="create-the-azure-stream-analytics-job"></a>Criar o trabalho Azure Stream Analytics

Define o trabalho stream Analytics no portal antes de o embalar como um módulo Edge.

1. No portal Azure, crie uma conta de armazenamento Azure utilizando as opções padrão no grupo de recursos **IoTEdgeDevices.** Tome nota do nome que escolheu.

1. No portal Azure, crie um **Stream Analytics Job** no grupo de recursos **IoTEdgeDevices.** Utilizar os seguintes valores de configuração:

    | Opção | Valor |
    | ------ | ----- |
    | Nome da tarefa | EdgeDeviceJob |
    | Subscrição | A sua subscrição do Azure |
    | Grupo de recursos | IoTEdgeDevices |
    | Localização | E.U.A Leste |
    | Ambiente de alojamento | Microsoft Edge |
    | Unidades de transmissão em fluxo | 1 |

1. Abra o trabalho **edgeDeviceJob** Stream Analytics no portal, clique em Entradas e adicione uma entrada de fluxo **edge hub** chamada **telemetria**.

1. No trabalho **edgeDeviceJob** Stream Analytics no portal, clique em **Saídas** e adicione uma saída **do Edge Hub** chamada **saída**.

1. No trabalho **edgeDeviceJob** Stream Analytics no portal, clique em **Saídas** e adicione uma segunda saída **do Edge Hub** chamada **alerta**.

1. No trabalho **edgeDeviceJob** Stream Analytics no portal, clique em **Consulta e** adicione a seguinte declaração **selecionada:**

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

1. No trabalho **edgeDeviceJob** Stream Analytics no portal, clique nas **definições da conta de armazenamento**. Adicione a conta de armazenamento que adicionou ao grupo de recursos **IoTEdgeDevices** como início desta secção. Criar um novo recipiente chamado **edgeconfig**.

A imagem a seguir mostra o trabalho de Stream Analytics guardado:

[![Trabalho de Stream Analytics](./media/iot-accelerators-remote-monitoring-edge/streamjob-inline.png)](./media/iot-accelerators-remote-monitoring-edge/streamjob-expanded.png#lightbox)

Definiu agora um trabalho de Stream Analytics para executar no seu dispositivo de borda. O trabalho calcula a temperatura média numa janela de 5 segundos. O trabalho também envia um alerta se a temperatura média numa janela de 3 segundos for superior a 400.

### <a name="create-the-iot-edge-deployment"></a>Criar a implementação IoT Edge

Em seguida, cria-se um manifesto de implantação IoT Edge que define os módulos a executar no seu dispositivo Edge. Na secção seguinte, importa este manifesto como um pacote na solução de Monitorização Remota.

1. No portal Azure, navegue para o hub IoT na sua solução de Monitorização Remota. Pode encontrar o hub IoT no grupo de recursos que tem o mesmo nome que a sua solução de Monitorização Remota.

1. No hub IoT, clique em **IoT Edge** na secção **De Gestão automática de Dispositivos.** Clique  **em Adicionar uma implementação IoT Edge**.

1. Na página **'Criar > Nome e Etiquetar'** e insira o **nome do dispositivo de bomba de óleo**. Clique em **Seguinte**.

1. Na página **'Criar implantação > Adicionar Módulos',** clique **+ Adicionar**. Escolha **o módulo IoT Edge**.

1. No painel **IoT Edge Custom Modules,** **introduza a temperaturaSensor** como nome, e **asaedgedockerhubtest/asa-edge-test-module:sensor-ad-linux-amd64** como URI de imagem. Clique em **Guardar**.

1. Na página **'Criar implantação' > adicionar módulos,** clique **+ Adicionar** para adicionar um segundo módulo. Escolha **o módulo Azure Stream Analytics**.

1. No painel **de implementação Edge,** selecione a sua subscrição e o **EdgeDeviceJob** que criou na secção anterior. Clique em **Guardar**.

1. Na página **'Criar > Adicionar Módulos',** clique em **Seguinte**.

1. Na página **'Criar > Especificar Rotas',** adicione o seguinte código:

    ```sql
    {
      "routes": { 
        "alertsToReset": "FROM /messages/modules/EdgeDeviceJob/outputs/alert INTO BrokeredEndpoint(\"/modules/temperatureSensor/inputs/control\")", 
        "telemetryToAsa": "FROM /messages/modules/temperatureSensor/* INTO BrokeredEndpoint(\"/modules/EdgeDeviceJob/inputs/telemetry\")", 
        "ASAToCloud": "FROM /messages/modules/EdgeDeviceJob/* INTO $upstream" 
      } 
    }
    ```

    Este código encaminha a saída do módulo Stream Analytics para as localizações corretas.

    Clique em **Seguinte**.

1. Na página **'Criar > Especificar métricas,'** clique em **Seguida**.

1. Na página **'Dispositivos alvo' > De Implementação, insira** 10 como prioridade. Clique em **Seguinte**.

1. Na página **'Criar >',** clique em **Enviar por ela' (implementação**>) clique em Submeter:

    [![Implementação de revisão](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-expanded.png#lightbox)

1. Na página principal do **IoT Edge,** clique nas **implementações do IoT Edge**. Pode ver **o dispositivo de bomba de óleo** na lista de implementações.

1. Clique na implementação do **dispositivo de bomba de óleo** e, em seguida, clique em Baixar O manifesto **IoT Edge**. Guarde o ficheiro à medida ** queoil-pump-device.js** para um local adequado na sua máquina local. Precisa deste ficheiro na próxima secção deste tutorial.

Criou agora um manifesto IoT Edge para importar para a solução de Monitorização Remota como um pacote. Normalmente, um desenvolvedor cria os módulos IoT Edge e ficheiro manifesto.

## <a name="import-a-package"></a>Importar um pacote

Nesta secção, importa o manifesto Edge como um pacote na solução de Monitorização Remota.

1. Na UI web de monitorização remota, navegue na página **Pacotes** e clique **em + Pacote Novo**:

    [![Novo pacote](./media/iot-accelerators-remote-monitoring-edge/newpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newpackage-expanded.png#lightbox)

1. No painel **New Package,** escolha **o Edge Manifest** como o tipo de pacote, clique em **Procurar** para encontrar aoil-pump-device.jsno ficheiro ** na** sua máquina local e clique em **Upload**:

    [![Pacote de upload](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-expanded.png#lightbox)

    A lista de pacotes inclui agora o **oil-pump-device.jsem** pacote.

Na secção seguinte, cria-se uma implementação que aplica a embalagem ao seu dispositivo Edge.

## <a name="deploy-a-package"></a>Implementar um pacote

Agora está pronto para colocar o pacote no seu dispositivo.

1. Na UI web de monitorização remota, navegue para a página **Implementações** e clique **em + Nova implementação**:

    [![Nova implantação](./media/iot-accelerators-remote-monitoring-edge/newdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newdeployment-expanded.png#lightbox)

1. No novo painel **de implantação,** crie uma implementação com as seguintes definições:

    | Opção | Valor |
    | ------ | ----- |
    | Nome   | OilPumpDevices |
    | Tipo de pacote | Manifesto da Borda |
    | Pacote | oil-pump-device.jsem |
    | Grupo de Dispositivos | OilPumps |
    | Prioridade | 10 |

    [![Criar implementação](./media/iot-accelerators-remote-monitoring-edge/createdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdeployment-expanded.png#lightbox)

    Clique em **Aplicar**.

É necessário esperar vários minutos para que a embalagem se desloque ao seu dispositivo e para que a telemetria comece a fluir do dispositivo.

[![Implementação ativa](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-inline.png)](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-expanded.png#lightbox)

A página **De Implementações** mostra as seguintes métricas:

* **O alvo** mostra o número de dispositivos no grupo de dispositivos.
* **A aplicação** mostra o número de dispositivos que tiveram o conteúdo de implantação aplicado.
* **O sucesso** mostra o número de dispositivos Edge na implementação reportando o sucesso do tempo de funcionamento do cliente IoT Edge.
* **O falhado** mostra o número de dispositivos Edge na falha de reporte de implementação do tempo de funcionamento do cliente IoT Edge.

## <a name="monitor-the-device"></a>Monitorize o dispositivo

Pode ver a telemetria de temperatura do seu dispositivo de bomba de óleo na UI web de monitorização remota:

1. Navegue na página **Device Explorer** e selecione o seu dispositivo de bomba de óleo.
1. Na secção **de telemetria** do painel de detalhes do **dispositivo,** clique em **Temperatura:**

    [![Ver telemetria](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-expanded.png#lightbox)

Pode ver como a temperatura sobe até atingir um limiar. O módulo Stream Analytics Edge deteta quando a temperatura atinge este limiar e envia um comando ao dispositivo para reduzir imediatamente a temperatura. Todo este processamento acontece no dispositivo sem comunicar com a nuvem.

Se pretender notificar os operadores quando o limiar foi atingido, pode criar uma regra na UI web de monitorização remota:

1. Navegue na página **Regras** e clique **em + Nova regra**.
1. Criar uma nova regra com as seguintes definições:

    | Opção | Valor |
    | ------ | ----- |
    | Nome da regra | Temperatura da bomba de óleo |
    | Descrição | Temperatura da bomba de óleo ultrapassou os 300 |
    | Grupo de dispositivos | OilPumps |
    | Cálculo | Instantâneo |
    | Campo | temperatura |
    | Operador | > |
    | Valor | 300 |
    | Nível de gravidade | Informações |

    [![Criar regra](./media/iot-accelerators-remote-monitoring-edge/newrule-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newrule-expanded.png#lightbox)

    Clique em **Aplicar**.

1. Navegue para a página **do Dashboard.** Um alerta mostra no painel **Alertas** quando a temperatura no dispositivo **da bomba de óleo** ultrapassa os 300.

## <a name="next-steps"></a>Passos seguintes

Este tutorial mostrou-lhe como adicionar e configurar um dispositivo IoT Edge no acelerador de solução de monitorização remota. Para saber mais sobre como trabalhar com pacotes IoT Edge na solução de Monitorização Remota, consulte o seguinte guia:

> [!div class="nextstepaction"]
> [Importe um pacote IoT Edge no seu acelerador de solução de monitorização remota](iot-accelerators-remote-monitoring-import-edge-package.md)

Para saber mais sobre a instalação do tempo de funcionamento do IoT Edge, consulte [instalar o tempo de execução Azure IoT Edge no Linux (x64)](../iot-edge/how-to-install-iot-edge-linux.md).

Para saber mais sobre o Azure Stream Analytics em dispositivos Edge, consulte [o Deploy Azure Stream Analytics como um módulo IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md).
