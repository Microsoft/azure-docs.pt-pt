---
title: Gerir dispositivos ligados a monitorização remota em massa – Azure | Documentos da Microsoft
description: Neste tutorial, saiba como gerir os dispositivos ligados a uma solução de monitorização remota em massa.
author: aditidugar
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: adugar
ms.openlocfilehash: 8a5c74c76662a089675fcbdcd8d5a7ea54b58fd1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799045"
---
# <a name="tutorial-manage-your-connected-devices-in-bulk"></a>Tutorial: Gerir os seus dispositivos ligados em massa

Neste tutorial, vai utilizar o acelerador de solução de monitorização remota para gerir a configuração dos seus dispositivos ligados em massa.

Como um operador na Contoso, tem de configurar um grupo de dispositivos com uma nova versão de firmware. Não quer ter que atualizar o firmware em cada dispositivo individualmente. Para atualizar o firmware num grupo de dispositivos, pode utilizar grupos de dispositivos e gestão de dispositivos automático no solution accelerator monitorização remota. Qualquer dispositivo que adicionar ao grupo de dispositivos obtém do firmware mais recente, assim que o dispositivo estiver online.

Neste tutorial:

>[!div class="checklist"]
> * Criar um grupo de dispositivos
> * Preparar e o firmware do anfitrião
> * Criar uma configuração de dispositivo no portal do Azure
> * Importar uma configuração de dispositivo para a sua solução de monitorização remota
> * Implementar a configuração para os dispositivos no grupo de dispositivos
> * Monitorizar a implementação

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

<!--
If this is going to be a tutorial - we need to split this include into two so that we can accommodate the additional prerequisites:

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]
-->

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este tutorial, precisa de uma instância implementada do acelerador de soluções de Monitorização remota na sua subscrição do Azure.

Se ainda não tiver implementado o acelerador de soluções de Monitorização Remota, deverá concluir o início rápido [Implementar uma solução de monitorização remota baseada na cloud](quickstart-remote-monitoring-deploy.md).

Precisa de uma conta de armazenamento do Azure para alojar os ficheiros de firmware. Pode utilizar uma conta de armazenamento existente, ou [criar uma nova conta de armazenamento](../storage/common/storage-quickstart-create-account.md) na sua subscrição.

O tutorial utiliza uma [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) dispositivo como um dispositivo de exemplo.

É necessário o seguinte software instalado no seu computador local:

* [Visual Studio Code (código de VS)](https://code.visualstudio.com/).
* O [Bancada de trabalho do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) extensão do VS Code.

Antes de começar:

* Certifique-se de que o [carregador de inicialização no seu dispositivo IoT DevKit está na versão 1.4.0 ou superior](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).
* Certifique-se de que o SDK do IoT DevKit tenha a mesma versão que o carregador de inicialização. Pode atualizar o SDK do IoT DevKit usando a Bancada de trabalho de IoT do Azure no VS Code. Abra a paleta de comandos e introduza **Arduino: Gestor de tabuleiro**. Para obter mais informações, consulte [preparar o ambiente de desenvolvimento](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment).

Também terá de ligar, pelo menos, um dispositivo de IoT DevKit ao seu acelerador de solução de monitorização remota. Se ainda não tiver ligado um dispositivo de IoT DevKit, consulte [ligar o MXChip IoT DevKit AZ3166 para o acelerador de solução de monitorização remota do IoT](iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md).

## <a name="navigate-to-the-dashboard"></a>Navegue para o dashboard

Para ver o dashboard da solução de Monitorização Remota no seu browser, navegue primeiro até [Aceleradores de Soluções IoT do Microsoft Azure](https://www.azureiotsolutions.com/Accelerators#dashboard). Poderá ser-lhe pedido para iniciar sessão com as credenciais da subscrição do Azure.

Em seguida, clique em **Iniciar** no mosaico do acelerador de soluções de Monitorização Remota que implementou no [Início Rápido](quickstart-remote-monitoring-deploy.md).

## <a name="create-a-device-group"></a>Criar um grupo de dispositivos

Para atualizar automaticamente o firmware num grupo de dispositivos, os dispositivos têm de ser membros de um grupo de dispositivos na sua solução de monitorização remota:

1. Na **dispositivos** , selecione todos os **IoT DevKit** dispositivos que ligou ao solution accelerator. Em seguida, clique em **Tarefas**.

1. Na **trabalhos** painel, selecione **etiquetas**, defina o nome da tarefa como **AddDevKitTag**e, em seguida, adicione uma etiqueta de texto chamada **IsDevKitDevice** com um valor **Y**. Em seguida, clique em **aplicar**.

1. Agora, pode utilizar os valores de etiqueta para criar um grupo de dispositivos. Sobre o **dispositivos** página, clique em **gerir grupos de dispositivos**.

1. Criar um filtro de texto que utiliza o nome de etiqueta **IsDevKitDevice** e o valor **Y** na condição. Guardar o grupo de dispositivos como **dispositivos de IoT DevKit**.

Mais tarde neste tutorial, vai utilizar este grupo de dispositivos para aplicar uma configuração de dispositivo que atualiza o firmware de todos os membros.

## <a name="prepare-and-host-the-firmware"></a>Preparar e o firmware do anfitrião

O [Bancada de trabalho do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) extensão do VS Code inclui o código de dispositivo de exemplo para a atualização de firmware.

### <a name="open-the-firmware-ota-sample-in-vs-code"></a>Abra o exemplo de Firmware OTA no VS Code

1. Certifique-se de que o seu IoT DevKit não está ligado ao seu computador. Inicie o VS Code e, em seguida, ligue o DevKit para o seu computador.

1. Prima **F1** para abrir a paleta de comandos, escreva e selecione **Bancada de trabalho de IoT: Exemplos**. Em seguida, selecione **IoT DevKit** como o quadro.

1. Encontrar **Firmware OTA** e clique em **exemplo aberto**. Uma nova janela do VS Code é aberto e mostra os **firmware_ota** pasta do projeto:

    ![Bancada de trabalho de IoT, selecione exemplo de Firmware OTA](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-firmware-example.png)

### <a name="build-the-new-firmware"></a>Criar o novo firmware

A versão inicial do firmware do dispositivo for 1.0.0. O novo firmware deve ter um número de versão superior.

1. No VS Code, abra a **FirmwareOTA.ino** de ficheiros e alterar o `currentFirmwareVersion` partir `1.0.0` para `1.0.1`:

    ![Alterar versão do firmware](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Abra a paleta de comandos, em seguida, escreva e selecione **Bancada de trabalho de IoT: Dispositivo**. Em seguida, selecione **dispositivo compilar** para compilar o código:

    ![Compilação do dispositivo](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-compile.png)

    Código VS salva o arquivo compilado no `.build` pasta no projeto. Dependendo das suas definições, o VS Code podem ocultar o `.build` pasta na vista do Explorador.

### <a name="generate-the-crc-value-and-calculate-the-firmware-file-size"></a>Gerar o valor CRC e calcular o tamanho de ficheiro do firmware

1. Abra a paleta de comandos, em seguida, escreva e selecione **Bancada de trabalho de IoT: Dispositivo**. Em seguida, selecione **gerar CRC**:

    ![Gerar CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-crc.png)

1. O VS Code gera e imprime o valor CRC, o nome de ficheiro do firmware e caminho e o tamanho do arquivo na janela de saída. Tome nota destes valores para utilizar mais tarde:

    ![Informações CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/crc-info.png)

### <a name="upload-the-firmware-to-the-cloud"></a>Carregar o firmware para a nuvem

Utilize a sua conta de armazenamento do Azure para alojar o novo arquivo de firmware na cloud.

1. Navegue para a sua conta de armazenamento no portal do Azure. Na secção de serviços, selecione **Blobs**. Criar um contentor público chamado **firmware** para armazenar os ficheiros de firmware:

    ![Criar Pasta](media/iot-accelerators-remote-monitoring-bulk-configuration-update/blob-folder.png)

1. Para carregar o ficheiro de firmware para o contentor, selecione o **firmware** contentor e clique em **carregar**.

1. Selecione o **FirmwareOTA.ino.bin**. Anotou o caminho completo para este ficheiro na secção anterior.

1. Depois do ficheiro de firmware carregamento concluído, anote o URL do ficheiro.

### <a name="build-and-upload-the-original-firmware-to-the-iot-devkit-device"></a>Criar e carregar o firmware original para o dispositivo de IoT DevKit

1. No VS Code, abra a **FirmwareOTA.ino** de ficheiros e alterar o `currentFirmwareVersion` novamente para `1.0.0`:

    ![Versão 1.0.0](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Abra a paleta de comandos, em seguida, escreva e selecione **Bancada de trabalho de IoT: Dispositivo**. Em seguida, selecione **carregamento de dispositivo**:

    ![Carregamento de dispositivo](media/iot-accelerators-remote-monitoring-bulk-configuration-update/device-upload.png)

1. O VS Code verifica e carrega o código para o seu dispositivo IoT DevKit.

1. Quando o carregamento estiver concluído, o dispositivo de IoT DevKit é reiniciado. Quando o reinício estiver concluído, o ecrã de IoT DevKit mostra **versão FW: 1.0.0**, e que está a verificar novo firmware:

    ![ota-1](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-1.jpg)

## <a name="create-a-device-configuration"></a>Criar uma configuração de dispositivo

Uma configuração de dispositivo Especifica o estado pretendido dos seus dispositivos. Normalmente, um desenvolvedor [cria a configuração](../iot-hub/iot-hub-automatic-device-management.md#create-a-configuration) sobre o **configuração do dispositivo IoT** página no portal do Azure. Uma configuração de dispositivo é um documento JSON que especifica o estado pretendido dos seus dispositivos e um conjunto de métricas.

Guardar a configuração seguinte como ficheiro chamado **firmware update.json** no seu computador local. Substitua a `YOURSTRORAGEACCOUNTNAME`, `YOURCHECKSUM`, e `YOURPACKAGESIZE` marcadores de posição pelos valores que anotou anteriormente:

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

Nesta secção, importe a configuração do dispositivo como um pacote para o acelerador de solução de monitorização remota. Normalmente, um operador concluir esta tarefa.

1. Na monitorização remota da IU da web, navegue para o **pacotes** página e clique em **+ novo pacote**:

    ![Novo pacote](media/iot-accelerators-remote-monitoring-bulk-configuration-update/packagepage.png)

1. Na **novo pacote** painel, escolha **configuração do dispositivo** como o tipo de pacote e **Firmware** como o tipo de configuração. Clique em **navegue** para localizar o **firmware update.json** de ficheiros no seu computador local e, em seguida, clique em **carregar**:

    ![Carregar o pacote](media/iot-accelerators-remote-monitoring-bulk-configuration-update/uploadpackage.png)

1. A lista de pacotes agora inclui a **atualização de firmware** pacote.

## <a name="deploy-the-configuration-to-your-devices"></a>Implementar a configuração nos seus dispositivos

Nesta secção, pode criar e executar uma implementação que se aplica a configuração do dispositivo para os seus dispositivos IoT DevKit.

1. Na monitorização remota da IU da web, navegue para o **implementações** página e clique em **+ nova implementação**:

    ![Nova implementação](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentpage.png)

1. Na **nova implementação** painel, pode criar uma implementação com as seguintes definições:

    |Opção|Valor|
    |---|---|
    |Nome|Implementar atualização de firmware|
    |Tipo de pacote|Configuração do Dispositivo|
    |Tipo de configuração|Firmware|
    |Pacote|firmware-update.json|
    |Grupo de dispositivos|Dispositivos de IoT DevKit|
    |Prioridade|10|

    ![Criar implementação](media/iot-accelerators-remote-monitoring-bulk-configuration-update/newdeployment.png)

    Clique em **Aplicar**. Verá uma nova implementação no **implementações** página que mostra as métricas seguintes:

    * **Direcionada** mostra o número de dispositivos no grupo de dispositivos.
    * **Aplicado** mostra o número de dispositivos que foram atualizados com o conteúdo de configuração.
    * **Foi efetuada com êxito** mostra o número de dispositivos na implementação de êxito desse relatório.
    * **Falha ao** mostra o número de dispositivos na implementação falha desse relatório.

## <a name="monitor-the-deployment"></a>Monitorizar a implementação

Após alguns minutos, o IoT DevKit obtém as novas informações de firmware e começa a baixá-lo para o dispositivo:

![ota-2](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-2.jpg)

Consoante a velocidade da sua rede, a transferência pode demorar alguns minutos. Depois do firmware é transferido, o dispositivo verifica o tamanho do ficheiro e o valor CRC. Apresenta o ecrã o MXChip **transmitido** se a verificação for bem sucedida.

![ota-3](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-3.jpg)

Se a verificação for bem sucedida, o dispositivo é reiniciado. Verá uma contagem regressiva partir **5** para **0** antes do reinício acontece.

![ota-4](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-4.jpg)

Após o reinício, o carregador de inicialização de IoT DevKit atualiza o firmware para a nova versão. A atualização poderá demorar vários segundos. Durante esta fase, o RGB LED no dispositivo está vermelha e a tela fique em branco.

![ota-5](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-5.jpg)

Quando o reinício estiver concluído, o dispositivo de IoT DevKit agora está a executar a versão 1.0.1 do firmware.

![ota-6](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-6.jpg)

Sobre o **implementações** página, clique numa implementação para ver o estado dos seus dispositivos, como eles de atualização. Pode ver o estado de cada dispositivo no seu grupo de dispositivos e as métricas personalizadas que definiu.

![Detalhes de implementação](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentstatus.png)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Este tutorial mostrou como atualizar o firmware de um grupo de dispositivos ligados à sua solução. Para atualizar os dispositivos, sua solução utiliza a gestão de dispositivos automática. Para saber mais sobre a funcionalidade de gerenciamento automático de dispositivos no hub de IoT subjacente da sua solução, veja [configurar e monitorizar dispositivos de IoT em escala no portal do Azure](../iot-hub/iot-hub-auto-device-config.md).