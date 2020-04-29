---
title: Gerir dispositivos ligados à Monitorização Remota a granel - Azure [ Microsoft Docs
description: Neste tutorial aprende-se a gerir os dispositivos ligados a uma solução de Monitorização Remota a granel.
author: Philmea
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: philmea
ms.openlocfilehash: eaca93ac8a4e8c660be9618aefb27921a4e0a2eb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77565583"
---
# <a name="tutorial-manage-your-connected-devices-in-bulk"></a>Tutorial: Gerencie os seus dispositivos conectados a granel

Neste tutorial, utiliza o acelerador de solução de monitorização remota para gerir a configuração dos seus dispositivos conectados a granel.

Como operador na Contoso, é necessário configurar um grupo de dispositivos com uma nova versão de firmware. Não pretende ter de atualizar o firmware em cada dispositivo individualmente. Para atualizar o firmware num conjunto de dispositivos, pode utilizar grupos de dispositivos e gestão automática de dispositivos no acelerador de soluções de monitorização remota. Qualquer dispositivo que adicione ao grupo de dispositivos recebe o mais recente firmware assim que o dispositivo estiver online.

Neste tutorial:

>[!div class="checklist"]
> * Criar um grupo de dispositivos
> * Prepare e acolde o firmware
> * Criar uma configuração de dispositivo no portal Azure
> * Importe uma configuração do dispositivo para a sua solução de monitorização remota
> * Implementar a configuração para os dispositivos no grupo do dispositivo
> * Monitorizar a implementação

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

<!--
If this is going to be a tutorial - we need to split this include into two so that we can accommodate the additional prerequisites:

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]
-->

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este tutorial, precisa de uma instância implementada do acelerador de soluções de Monitorização remota na sua subscrição do Azure.

Se ainda não tiver implementado o acelerador de soluções de Monitorização Remota, deverá concluir o início rápido [Implementar uma solução de monitorização remota baseada na cloud](quickstart-remote-monitoring-deploy.md).

Precisa de uma conta de armazenamento Azure para alojar os seus ficheiros de firmware. Pode utilizar uma conta de armazenamento existente ou [criar uma nova conta](../storage/common/storage-account-create.md) de armazenamento na sua subscrição.

O tutorial utiliza um dispositivo [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) como dispositivo de amostra.

Precisa do seguinte software instalado na sua máquina local:

* Código de [estúdio visual (Código VS)](https://code.visualstudio.com/).
* A extensão do Código VS da [bancada de trabalho Azure IoT.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench)

Antes de começar:

* Certifique-se de que o [bootloader do seu dispositivo IoT DevKit está na versão 1.4.0 ou superior](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).
* Certifique-se de que o IoT DevKit SDK está na mesma versão que o bootloader. Pode atualizar o IoT DevKit SDK utilizando a bancada de trabalho Azure IoT no Código VS. Abra a paleta de comando e entre **arduino: Gerente de Bordo.** Para mais informações, consulte [Preparar o ambiente de desenvolvimento.](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment)

Também precisa de ligar pelo menos um dispositivo IoT DevKit ao seu acelerador de solução de monitorização remota. Se não tiver ligado um dispositivo IoT DevKit, consulte [Connect MXChip IoT DevKit AZ3166 ao acelerador de solução de monitorização remota IoT](iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md).

## <a name="navigate-to-the-dashboard"></a>Navegue para o dashboard

Para ver o dashboard da solução de Monitorização Remota no seu browser, navegue primeiro até [Aceleradores de Soluções IoT do Microsoft Azure](https://www.azureiotsolutions.com/Accelerators#dashboard). Poderá ser-lhe pedido para iniciar sessão com as credenciais da subscrição do Azure.

Em seguida, clique em **Iniciar** no mosaico do acelerador de soluções de Monitorização Remota que implementou no [Início Rápido](quickstart-remote-monitoring-deploy.md).

## <a name="create-a-device-group"></a>Criar um grupo de dispositivos

Para atualizar automaticamente o firmware num grupo de dispositivos, os dispositivos devem ser membros de um grupo de dispositivos na sua solução de Monitorização Remota:

1. Na página **Dispositivos,** selecione todos os dispositivos **IoT DevKit** que ligou ao acelerador de soluções. Em seguida, clique em **Jobs**.

1. No painel **Jobs,** selecione **Tags,** detete o nome de trabalho para **AddDevKitTag,** e adicione uma etiqueta de texto chamada **IsDevKitDevice** com um valor **Y**. Em seguida, clique em **Aplicar**.

1. Agora pode usar os valores de etiqueta para criar um grupo de dispositivos. Na página **Dispositivos,** clique em **Gerir grupos de dispositivos**.

1. Crie um filtro de texto que utilize o nome de etiqueta **IsDevKitDevice** e valorize **Y** na condição. Guarde o grupo do dispositivo como **dispositivos IoT DevKit**.

Mais tarde neste tutorial, utiliza este grupo de dispositivos para aplicar uma configuração do dispositivo que atualiza o firmware de todos os membros.

## <a name="prepare-and-host-the-firmware"></a>Prepare e acolde o firmware

A extensão do Código VS da [bancada de trabalho Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) inclui o código do dispositivo de amostra para a atualização do firmware.

### <a name="open-the-firmware-ota-sample-in-vs-code"></a>Abra a amostra Firmware OTA no Código VS

1. Certifique-se de que o seu IoT DevKit não está ligado ao seu computador. Inicie o Código VS e, em seguida, ligue o DevKit ao seu computador.

1. Prima **F1** para abrir a paleta de comando, digite e selecione **IoT Workbench: Exemplos**. Em seguida, selecione **IoT DevKit** como placa.

1. Encontre **firmware OTA** e clique em **Amostra Aberta**. Uma nova janela do Código VS abre e mostra a pasta do projeto **firmware_ota:**

    ![IoT Workbench, selecione Firmware OTA exemplo](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-firmware-example.png)

### <a name="build-the-new-firmware"></a>Construa o novo firmware

A versão inicial do firmware do dispositivo é de 1.0.0. O novo firmware deve ter um número de versão mais elevado.

1. No Código VS, abra o ficheiro **FirmwareOTA.ino** e altere o `currentFirmwareVersion` `1.0.0` `1.0.1`de:

    ![Alterar a versão firmware](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Abra a paleta de comando, em seguida, digite e selecione **IoT Workbench: Dispositivo**. Em seguida, **selecione Device Compile** para compilar o código:

    ![Compilação de dispositivos](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-compile.png)

    O Código VS guarda o `.build` ficheiro compilado na pasta do projeto. Dependendo das definições, o `.build` Código VS pode ocultar a pasta na vista do explorador.

### <a name="generate-the-crc-value-and-calculate-the-firmware-file-size"></a>Gere o valor CRC e calcule o tamanho do ficheiro firmware

1. Abra a paleta de comando, em seguida, digite e selecione **IoT Workbench: Dispositivo**. Em seguida, **selecione Generate CRC:**

    ![Gerar CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-crc.png)

1. O Código VS gera e imprime o valor CRC, o nome de ficheiro e o caminho do firmware e o tamanho do ficheiro na janela de saída. Tome nota destes valores para mais tarde:

    ![Informação do CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/crc-info.png)

### <a name="upload-the-firmware-to-the-cloud"></a>Faça upload do firmware para a nuvem

Utilize a sua conta de armazenamento Azure para alojar o seu novo ficheiro firmware na nuvem.

1. Navegue para a sua conta de armazenamento no portal do Azure. Na secção Serviços, selecione **Blobs**. Crie um recipiente público chamado **firmware** para armazenar os seus ficheiros firmware:

    ![Criar Pasta](media/iot-accelerators-remote-monitoring-bulk-configuration-update/blob-folder.png)

1. Para fazer o upload do ficheiro firmware para o recipiente, selecione o recipiente **firmware** e clique em **Carregar**.

1. Selecione o **FirmwareOTA.ino.bin**. Fez uma nota do caminho completo para este ficheiro na secção anterior.

1. Depois de o ficheiro firmware ser carregado completo, tome nota do URL do ficheiro.

### <a name="build-and-upload-the-original-firmware-to-the-iot-devkit-device"></a>Construa e carregue o firmware original para o dispositivo IoT DevKit

1. No Código VS, abra o ficheiro **FirmwareOTA.ino** e mude o `currentFirmwareVersion` back para: `1.0.0`

    ![Versão 1.0.0](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Abra a paleta de comando, em seguida, digite e selecione **IoT Workbench: Dispositivo**. Em seguida, selecione **O Upload do Dispositivo:**

    ![Upload do dispositivo](media/iot-accelerators-remote-monitoring-bulk-configuration-update/device-upload.png)

1. O Código VS verifica e envia o código para o seu dispositivo IoT DevKit.

1. Quando o upload terminar, o dispositivo IoT DevKit reinicia. Quando o reboot está concluído, o ecrã do IoT DevKit mostra a **versão FW: 1.0.0**, e que está a verificar novos firmware:

    ![ota-1](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-1.jpg)

## <a name="create-a-device-configuration"></a>Criar uma configuração de dispositivo

Uma configuração do dispositivo especifica o estado desejado dos seus dispositivos. Tipicamente, um desenvolvedor [cria a configuração](../iot-hub/iot-hub-automatic-device-management.md#create-a-configuration) na página de configuração do **dispositivo IoT** no portal Azure. Uma configuração do dispositivo é um documento JSON que especifica o estado desejado dos seus dispositivos e um conjunto de métricas.

Guarde a seguinte configuração como ficheiro chamado **firmware-update.json** na sua máquina local. Substitua `YOURSTRORAGEACCOUNTNAME` `YOURCHECKSUM`os `YOURPACKAGESIZE` , e os espaços reservados com os valores que fez anteriormente:

```json
{
  "id": "firmware-update",
  "schemaVersion": null,
  "labels": {
    "Version": "1.0.1",
    "Devices": "IoT DevKit"
  },
  "content": {
    "deviceContent": {
      "properties.desired.firmware": {
        "fwVersion": "1.0.1",
        "fwPackageURI": "https://YOURSTRORAGEACCOUNTNAME.blob.core.windows.net/firmware/FirmwareOTA.ino.bin",
        "fwPackageCheckValue": "YOURCHECKSUM",
        "fwSize": YOURPACKAGESIZE
      }
    }
  },
  "targetCondition": "",
  "priority": 10,
  "systemMetrics": {
    "results": {
      "targetedCount": 0,
      "appliedCount": 0
    },
    "queries": {
      "targetedCount": "",
      "appliedCount": "select deviceId from devices where configurations.[[firmware-update]].status = 'Applied'"
    }
  },
  "metrics": {
    "results": {
      "Current": 1
    },
    "queries": {
      "Current": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Current' AND properties.reported.firmware.type='IoTDevKit'",
      "Downloading": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Downloading' AND properties.reported.firmware.type='IoTDevKit'",
      "Verifying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Verifying' AND properties.reported.firmware.type='IoTDevKit'",
      "Applying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Applying' AND properties.reported.firmware.type='IoTDevKit'",
      "Error": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Error' AND properties.reported.firmware.type='IoTDevKit'"
    }
  }
}
```

Utilize este ficheiro de configuração na secção seguinte.

## <a name="import-a-configuration"></a>Importar uma configuração

Nesta secção, importa-se a configuração do dispositivo como uma embalagem para o acelerador de solução de monitorização remota. Normalmente, um operador completa esta tarefa.

1. Na UI web de monitorização remota, navegue para a página **Pacotes** e clique **em + Novo Pacote:**

    ![Novo pacote](media/iot-accelerators-remote-monitoring-bulk-configuration-update/packagepage.png)

1. No painel **New Package,** escolha a **configuração do Dispositivo** como tipo de pacote e **Firmware** como o tipo de configuração. Clique em **Navegar** para encontrar o ficheiro **firmware-update.json** na sua máquina local e, em seguida, clique em **Carregar:**

    ![Pacote de upload](media/iot-accelerators-remote-monitoring-bulk-configuration-update/uploadpackage.png)

1. A lista de pacotes inclui agora o pacote **de atualização de firmware.**

## <a name="deploy-the-configuration-to-your-devices"></a>Implemente a configuração para os seus dispositivos

Nesta secção, cria e executa uma implementação que aplica a configuração do dispositivo aos seus dispositivos IoT DevKit.

1. Na UI web de monitorização remota, navegue para a página **de Implementações** e clique **+ Nova implementação:**

    ![Nova implantação](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentpage.png)

1. No novo painel de **implantação,** crie uma implementação com as seguintes definições:

    |Opção|Valor|
    |---|---|
    |Nome|Implementar atualização de firmware|
    |Tipo de pacote|Configuração do Dispositivo|
    |Tipo de configuração|Firmware|
    |Pacote|firmware-update.json|
    |Grupo de Dispositivos|Dispositivos IoT DevKit|
    |Prioridade|10|

    ![Criar implementação](media/iot-accelerators-remote-monitoring-bulk-configuration-update/newdeployment.png)

    Clique em **Aplicar**. Você vê uma nova implementação na página **de Implementações** que mostra as seguintes métricas:

    * **O alvo** mostra o número de dispositivos no grupo do dispositivo.
    * **Aplicado** mostra o número de dispositivos que foram atualizados com o conteúdo de configuração.
    * **O sucesso** mostra o número de dispositivos na implantação que reportam o sucesso.
    * **Falhas** mostram o número de dispositivos na implementação que reportam falha.

## <a name="monitor-the-deployment"></a>Monitorizar a implementação

Após alguns minutos, o IoT DevKit recupera as novas informações de firmware e começa a descarregá-la para o dispositivo:

![ota-2](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-2.jpg)

Dependendo da velocidade da sua rede, o download pode demorar até alguns minutos. Após o download do firmware, o dispositivo verifica o tamanho do ficheiro e o valor CRC. O ecrã dos ecrãs MXChip **passou** se a verificação for bem sucedida.

![ota-3](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-3.jpg)

Se a verificação for bem sucedida, o dispositivo reinicia. Vê-se uma contagem regressiva de **5** a **0** antes do reboot acontecer.

![ota-4](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-4.jpg)

Após o reboot, o bootloader IoT DevKit atualiza o firmware para a nova versão. A atualização pode demorar alguns segundos. Durante esta fase, o LED RGB no dispositivo é vermelho e o ecrã está em branco.

![ota-5](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-5.jpg)

Quando o reboot estiver concluído, o seu dispositivo IoT DevKit está agora a executar a versão 1.0.1 do firmware.

![ota-6](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-6.jpg)

Na página **'Implementações',** clique numa implementação para ver o estado dos seus dispositivos à medida que atualizam. Pode ver o estado de cada dispositivo no seu grupo de dispositivos e as métricas personalizadas que definiu.

![Detalhes de implantação](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentstatus.png)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Este tutorial mostrou-lhe como atualizar o firmware de um grupo de dispositivos ligados à sua solução. Para atualizar os dispositivos, a sua solução utiliza a gestão automática do dispositivo. Para saber mais sobre a funcionalidade de gestão automática do dispositivo no hub IoT subjacente da sua solução, consulte os [dispositivos Configure e monitor ioT em escala utilizando o portal Azure](../iot-hub/iot-hub-auto-device-config.md).