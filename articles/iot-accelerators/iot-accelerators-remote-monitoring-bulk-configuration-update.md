---
title: Gerenciar dispositivos conectados ao monitoramento remoto em massa – Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a gerenciar os dispositivos conectados a uma solução de monitoramento remoto em massa.
author: aditidugar
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: adugar
ms.openlocfilehash: 8ba2d4eca3287efc746c0d4902b6bcc4bd0c796e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980555"
---
# <a name="tutorial-manage-your-connected-devices-in-bulk"></a>Tutorial: gerenciar seus dispositivos conectados em massa

Neste tutorial, você usa o acelerador de solução de monitoramento remoto para gerenciar a configuração de seus dispositivos conectados em massa.

Como um operador na contoso, você precisa configurar um grupo de dispositivos com uma nova versão de firmware. Você não deseja ter que atualizar o firmware em cada dispositivo individualmente. Para atualizar o firmware em um grupo de dispositivos, você pode usar grupos de dispositivos e gerenciamento de dispositivo automático no acelerador de solução de monitoramento remoto. Qualquer dispositivo adicionado ao grupo de dispositivos Obtém o firmware mais recente assim que o dispositivo fica online.

Neste tutorial:

>[!div class="checklist"]
> * Criar um grupo de dispositivos
> * Preparar e hospedar o firmware
> * Criar uma configuração de dispositivo no portal do Azure
> * Importar uma configuração de dispositivo para sua solução de monitoramento remoto
> * Implantar a configuração nos dispositivos no grupo de dispositivos
> * Monitorizar a implementação

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

<!--
If this is going to be a tutorial - we need to split this include into two so that we can accommodate the additional prerequisites:

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]
-->

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este tutorial, precisa de uma instância implementada do acelerador de soluções de Monitorização remota na sua subscrição do Azure.

Se ainda não tiver implementado o acelerador de soluções de Monitorização Remota, deverá concluir o início rápido [Implementar uma solução de monitorização remota baseada na cloud](quickstart-remote-monitoring-deploy.md).

Você precisa de uma conta de armazenamento do Azure para hospedar seus arquivos de firmware. Você pode usar uma conta de armazenamento existente ou [criar uma nova conta de armazenamento](../storage/common/storage-account-create.md) em sua assinatura.

O tutorial usa um dispositivo [IOT devkit](https://microsoft.github.io/azure-iot-developer-kit/) como um dispositivo de exemplo.

Você precisa do seguinte software instalado em seu computador local:

* [Visual Studio Code (vs Code)](https://code.visualstudio.com/).
* A extensão de VS Code do [Azure IOT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) .

Antes de começar:

* Verifique se o carregador de início de [versão do dispositivo IOT devkit está no 1.4.0 ou superior](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).
* Verifique se o SDK do IoT DevKit está na mesma versão do carregador de paficação. Você pode atualizar o SDK do IoT DevKit usando o Azure IoT Workbench no VS Code. Abra a paleta de comandos e insira **Arduino: gerente de placa**. Para obter mais informações, consulte [preparar o ambiente de desenvolvimento](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment).

Você também precisa conectar pelo menos um dispositivo IoT DevKit ao acelerador de solução de monitoramento remoto. Se você não conectou um dispositivo IoT DevKit, consulte [conectar MXChip IOT DEVKIT AZ3166 ao acelerador de solução de monitoramento remoto de IOT](iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md).

## <a name="navigate-to-the-dashboard"></a>Navegue para o dashboard

Para ver o dashboard da solução de Monitorização Remota no seu browser, navegue primeiro até [Aceleradores de Soluções IoT do Microsoft Azure](https://www.azureiotsolutions.com/Accelerators#dashboard). Poderá ser-lhe pedido para iniciar sessão com as credenciais da subscrição do Azure.

Em seguida, clique em **Iniciar** no mosaico do acelerador de soluções de Monitorização Remota que implementou no [Início Rápido](quickstart-remote-monitoring-deploy.md).

## <a name="create-a-device-group"></a>Criar um grupo de dispositivos

Para atualizar automaticamente o firmware em um grupo de dispositivos, os dispositivos devem ser membros de um grupo de dispositivos em sua solução de monitoramento remoto:

1. Na página **dispositivos** , selecione todos os dispositivos **IOT devkit** que você conectou ao Solution Accelerator. Em seguida, clique em **Tarefas**.

1. No painel **trabalhos** , selecione **marcas**, defina o nome do trabalho como **AddDevKitTag**e, em seguida, adicione uma marca de texto chamada **IsDevKitDevice** com um valor **Y**. Em seguida, clique em **aplicar**.

1. Agora você pode usar os valores de marca para criar um grupo de dispositivos. Na página **dispositivos** , clique em **gerenciar grupos de dispositivos**.

1. Crie um filtro de texto que use o nome de marca **IsDevKitDevice** e o valor **Y** na condição. Salve o grupo de dispositivos como **dispositivos IOT devkit**.

Posteriormente neste tutorial, você usará esse grupo de dispositivos para aplicar uma configuração de dispositivo que atualiza o firmware de todos os membros.

## <a name="prepare-and-host-the-firmware"></a>Preparar e hospedar o firmware

A extensão de VS Code do [Azure IOT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) inclui o código do dispositivo de exemplo para a atualização do firmware.

### <a name="open-the-firmware-ota-sample-in-vs-code"></a>Abra o exemplo de firmware OTA no VS Code

1. Verifique se o IoT DevKit não está conectado ao computador. Inicie o VS Code e conecte o DevKit ao seu computador.

1. Pressione **F1** para abrir a paleta de comandos, digite e selecione **IOT Workbench: exemplos**. Em seguida, selecione **IOT devkit** como o quadro.

1. Localize o **firmware OTA** e clique em **abrir exemplo**. Uma nova janela VS Code é aberta e mostra a pasta **firmware_ota** projeto:

    ![IoT Workbench, selecione o exemplo de firmware OTA](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-firmware-example.png)

### <a name="build-the-new-firmware"></a>Criar o novo firmware

A versão inicial do firmware do dispositivo é a 1.0.0. O novo firmware deve ter um número de versão mais alto.

1. No VS Code, abra o arquivo **FirmwareOTA. ino** e altere o `currentFirmwareVersion` de `1.0.0` para `1.0.1`:

    ![Alterar a versão do firmware](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Abra a paleta de comandos e digite e selecione **IOT Workbench: dispositivo**. Em seguida, selecione **dispositivo compilar** para compilar o código:

    ![Compilação de dispositivo](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-compile.png)

    VS Code salva o arquivo compilado na pasta `.build` no projeto. Dependendo de suas configurações, VS Code poderá ocultar a pasta `.build` no modo de exibição do Explorer.

### <a name="generate-the-crc-value-and-calculate-the-firmware-file-size"></a>Gerar o valor de CRC e calcular o tamanho do arquivo de firmware

1. Abra a paleta de comandos e digite e selecione **IOT Workbench: dispositivo**. Em seguida, selecione **gerar CRC**:

    ![Gerar CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-crc.png)

1. VS Code gera e imprime o valor de CRC, o nome de arquivo e o caminho do firmware e o tamanho do arquivo na janela saída. Anote esses valores para mais tarde:

    ![Informações de CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/crc-info.png)

### <a name="upload-the-firmware-to-the-cloud"></a>Carregar o firmware para a nuvem

Use sua conta de armazenamento do Azure para hospedar o novo arquivo de firmware na nuvem.

1. Navegue para a sua conta de armazenamento no portal do Azure. Na seção serviços, selecione **BLOBs**. Crie um contêiner público chamado **firmware** para armazenar seus arquivos de firmware:

    ![Criar pasta](media/iot-accelerators-remote-monitoring-bulk-configuration-update/blob-folder.png)

1. Para carregar o arquivo de firmware no contêiner, selecione o contêiner de **firmware** e clique em **carregar**.

1. Selecione o **FirmwareOTA. ino. bin**. Você fez uma observação do caminho completo para esse arquivo na seção anterior.

1. Após a conclusão do upload do arquivo de firmware, anote a URL do arquivo.

### <a name="build-and-upload-the-original-firmware-to-the-iot-devkit-device"></a>Compilar e carregar o firmware original para o dispositivo IoT DevKit

1. No VS Code, abra o arquivo **FirmwareOTA. ino** e altere o `currentFirmwareVersion` de volta para `1.0.0`:

    ![Versão 1.0.0](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Abra a paleta de comandos e digite e selecione **IOT Workbench: dispositivo**. Em seguida, selecione **carregar dispositivo**:

    ![Upload do dispositivo](media/iot-accelerators-remote-monitoring-bulk-configuration-update/device-upload.png)

1. VS Code verifica e carrega o código para o dispositivo IoT DevKit.

1. Quando o upload é concluído, o dispositivo IoT DevKit é reinicializado. Quando a reinicialização for concluída, a tela de IoT DevKit mostrará a **versão do FW: 1.0.0**e a verificação de novo firmware:

    ![ota-1](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-1.jpg)

## <a name="create-a-device-configuration"></a>Criar uma configuração de dispositivo

Uma configuração de dispositivo especifica o estado desejado de seus dispositivos. Normalmente, um desenvolvedor [cria a configuração](../iot-hub/iot-hub-automatic-device-management.md#create-a-configuration) na página de **configuração do dispositivo IOT** no portal do Azure. Uma configuração de dispositivo é um documento JSON que especifica o estado desejado de seus dispositivos e um conjunto de métricas.

Salve a configuração a seguir como arquivo chamado **Firmware-Update. JSON** em seu computador local. Substitua os espaços reservados `YOURSTRORAGEACCOUNTNAME`, `YOURCHECKSUM`e `YOURPACKAGESIZE` pelos valores que você fez anotar anteriormente:

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

Use esse arquivo de configuração na seção a seguir.

## <a name="import-a-configuration"></a>Importar uma configuração

Nesta seção, você importará a configuração do dispositivo como um pacote no acelerador de solução de monitoramento remoto. Normalmente, um operador conclui essa tarefa.

1. Na interface do usuário da Web de monitoramento remoto, navegue até a página **pacotes** e clique em **+ novo pacote**:

    ![Novo pacote](media/iot-accelerators-remote-monitoring-bulk-configuration-update/packagepage.png)

1. No painel **novo pacote** , escolha **configuração do dispositivo** como o tipo de pacote e **firmware** como o tipo de configuração. Clique em **procurar** para localizar o arquivo **Firmware-Update. JSON** em seu computador local e, em seguida, clique em **carregar**:

    ![Carregar pacote](media/iot-accelerators-remote-monitoring-bulk-configuration-update/uploadpackage.png)

1. A lista de pacotes agora inclui o pacote de **atualização de firmware** .

## <a name="deploy-the-configuration-to-your-devices"></a>Implantar a configuração em seus dispositivos

Nesta seção, você cria e executa uma implantação que aplica a configuração do dispositivo aos dispositivos IoT DevKit.

1. Na interface do usuário da Web de monitoramento remoto, navegue até a página **implantações** e clique em **+ nova implantação**:

    ![Nova implantação](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentpage.png)

1. No novo painel de **implantação** , crie uma implantação com as seguintes configurações:

    |Opção|Valor|
    |---|---|
    |Nome|Implantar atualização de firmware|
    |Tipo de pacote|Configuração do Dispositivo|
    |Tipo de configuração|Firmware|
    |Pacote|firmware-update.json|
    |Grupo de dispositivos|Dispositivos IoT DevKit|
    |Prioridade|10|

    ![Criar implementação](media/iot-accelerators-remote-monitoring-bulk-configuration-update/newdeployment.png)

    Clique em **Aplicar**. Você verá uma nova implantação na página **implantações** que mostra as seguintes métricas:

    * **Direcionado** mostra o número de dispositivos no grupo de dispositivos.
    * **Aplicado** mostra o número de dispositivos que foram atualizados com o conteúdo de configuração.
    * **Êxito** mostra o número de dispositivos na implantação que relatam êxito.
    * **Falha** mostra o número de dispositivos na implantação que relatam falha.

## <a name="monitor-the-deployment"></a>Monitorizar a implementação

Após alguns minutos, o IoT DevKit recupera as novas informações de firmware e começa a baixá-las para o dispositivo:

![ota-2](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-2.jpg)

Dependendo da velocidade da sua rede, o download pode levar alguns minutos para demorar. Depois que o firmware é baixado, o dispositivo verifica o tamanho do arquivo e o valor de CRC. A tela no MXChip exibirá a **aprovação** se a verificação for bem-sucedida.

![ota-3](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-3.jpg)

Se a verificação for bem-sucedida, o dispositivo será reinicializado. Você verá uma contagem regressiva de **5** a **0** antes que a reinicialização ocorra.

![ota-4](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-4.jpg)

Após a reinicialização, o carregador de inicialização IoT DevKit atualiza o firmware para a nova versão. A atualização pode levar vários segundos. Durante esse estágio, o LED RGB no dispositivo é vermelho e a tela fica em branco.

![OTA-5](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-5.jpg)

Quando a reinicialização for concluída, seu dispositivo IoT DevKit agora está executando a versão 1.0.1 do firmware.

![ota-6](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-6.jpg)

Na página **implantações** , clique em uma implantação para ver o status dos dispositivos à medida que eles são atualizados. Você pode ver o status de cada dispositivo em seu grupo de dispositivos e as métricas personalizadas que você definiu.

![Detalhes de implementação](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentstatus.png)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Este tutorial mostrou como atualizar o firmware de um grupo de dispositivos conectados à sua solução. Para atualizar os dispositivos, sua solução usa o gerenciamento automático de dispositivos. Para saber mais sobre o recurso de gerenciamento de dispositivo automático no Hub IoT subjacente da solução, confira [configurar e monitorar dispositivos IOT em escala usando o portal do Azure](../iot-hub/iot-hub-auto-device-config.md).