---
title: Tutorial - Criar uma instância de IoT Edge de análise de vídeo em Azure IoT Central (Intel NUC)
description: Este tutorial mostra como criar uma instância IoT Edge de análise de vídeo para usar com o modelo de aplicação de deteção de objetos e movimentos.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/27/2020
ms.openlocfilehash: b74c7c3e9678c89edbe90d648520b9526c8fb569
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92748657"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-intel-nuc"></a>Tutorial: Criar uma instância IoT Edge para análise de vídeo (Intel NUC)

Azure IoT Edge é um serviço totalmente gerido que fornece inteligência em nuvem localmente, implantando e executando:

* Lógica personalizada
* Serviços do Azure
* Inteligência artificial

No IoT Edge, estes serviços funcionam diretamente em dispositivos IoT de plataforma cruzada, permitindo-lhe executar a sua solução IoT de forma segura e em escala na nuvem ou offline.

Este tutorial mostra-lhe como instalar e configurar o tempo de execução do IoT Edge num dispositivo Intel NUC.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Atualizar e configurar IoT Edge
> * Configurar o gateway IoT Edge
> * Ligue uma câmara local compatível com ONVIF ao seu dispositivo Intel NUC

## <a name="prerequisites"></a>Pré-requisitos

* Antes de começar, deverá completar a anterior [Criar uma aplicação de análise de vídeo ao vivo no Azure IoT Central (YOLO v3)](./tutorial-video-analytics-create-app-yolo-v3.md) ou criar uma análise de vídeo no tutorial [Azure IoT Central (OpenVINO). &trade; ](tutorial-video-analytics-create-app-openvino.md)
* Um dispositivo, como um Intel NUC, que executa o Linux, que pode executar contentores Docker, e tem poder de processamento suficiente para executar análises de vídeo.
* O [tempo de funcionamento IoT Edge instalado](../../iot-edge/how-to-install-iot-edge.md) e em funcionamento no dispositivo.
* Seja capaz de ligar ao dispositivo IoT Edge a partir da sua máquina Windows, precisa do [cliente PuTTY SSH](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) ou de um utilitário equivalente.
* Também precisa de uma assinatura Azure. Se não tiver uma subscrição do Azure, pode criar uma gratuitamente na página de inscrição do [Azure](https://aka.ms/createazuresubscription).

## <a name="configure-the-iot-edge-device"></a>Configurar o dispositivo do IoT Edge

Se não tiver o tempo de funcionamento IoT Edge instalado na sua máquina Intel NUC, consulte [instalar o tempo de funcionamento do Azure IoT Edge nas instruções dos sistemas Linux baseados em Debian.](../../iot-edge/how-to-install-iot-edge.md)

Para atualizar o tempo de execução do IoT Edge:

1. Utilize o utilitário PuTTY para ligar ao dispositivo IoT Edge.

1. Execute os seguintes comandos para atualizar e verificar a versão do tempo de execução IoT Edge. No momento da escrita, a versão é 1.0.9:

    ```bash
    sudo apt-get update
    sudo apt-get install libiothsm iotedge
    sudo iotedge --version
    ```

1. Utilize os seguintes comandos para criar as pastas que a implementação utiliza com as permissões necessárias:

    ```bash
    sudo mkdir -p /data/storage
    sudo mkdir -p /data/media
    sudo chmod -R 777 /data
    ```

Para adicionar o *state.jsno* ficheiro de configuração à pasta */dados/armazenamento* no seu dispositivo IoT Edge:

1. Utilize um editor de texto para abrir o *state.jsficheiro* na pasta *de configuração Lva* na sua máquina local.

1. Atualize os `system` espaços reservados e `iotCentral > properties` os espaços reservados com valores de cadeia que descrevem o seu dispositivo gateway. Pode ver estes valores mais tarde no painel de aplicações IoT Central.

1. Atualize os `iotCentral > appKeys` espaços reservados com os valores que fez no ficheiro *scratchpad.txt* no tutorial anterior. Guarde as alterações.

1. Utilize o utilitário PuTTY `scp` num pedido de comando para copiar ostate.js *no* ficheiro que acaba de editar para */pasta de armazenamento /dados/armazenamento* no seu dispositivo IoT Edge. Este exemplo utiliza `192.168.0.144` como exemplo endereço IP, substitua-o pelo endereço IP do seu dispositivo IoT Edge:

    ```cmd
    scp state.json YourUserName@192.168.0.144:/data/storage/state.json`
    ```

Configure a IoT Edge para registar e ligar à sua aplicação IoT Central:

1. Utilize o utilitário PuTTY para ligar ao dispositivo IoT Edge.

1. Utilize um editor de texto, `nano` como, para abrir o ficheiro IoT Edge config.yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

    > [!WARNING]
    > Os ficheiros YAML não podem usar separadores para entalhes, utilize dois espaços em vez disso. Itens de alto nível não podem ter espaço em branco líder.

1. Desça até `# Manual provisioning configuration` ver. Comente as próximas três linhas, como mostrado no seguinte corte:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "temp"
    ```

1. Desça até `# DPS symmetric key provisioning configuration` ver. Descomprometar as próximas oito linhas, como mostrado no seguinte corte:

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

1. `{scope_id}`Substitua-o pelo **ID Scope** que fez uma nota no ficheiro *scratchpad.txt* no tutorial anterior.

1. Substitua `{registration_id}` *por Lva-gateway-001,* o dispositivo que criou no tutorial anterior.

1. `{symmetric_key}`Substitua-a pela **tecla Primária** para o dispositivo **Lva-gateway-001** que fez uma nota no ficheiro *scratchpad.txt* no tutorial anterior.

1. Executar o seguinte comando para reiniciar o daemon IoT Edge:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Para verificar o estado dos módulos IoT Edge, execute o seguinte comando:

    ```bash
    iotedge list
    ```

    A saída do comando pervioso mostra cinco módulos de execução. Também pode ver o estado dos módulos de funcionamento na sua aplicação IoT Central.

    > [!TIP]
    > Pode repetir este comando para verificar o estado. Pode ter de esperar que todos os módulos comecem a funcionar.

Se os módulos IoT Edge não começarem corretamente, consulte [Troubleshoot o seu dispositivo IoT Edge](../../iot-edge/troubleshoot.md).

## <a name="collect-the-rtsp-stream-from-your-camera"></a>Recolha o fluxo RTSP da sua câmara

Identifique os URLs de fluxo RTSP para as câmaras ligadas ao seu dispositivo IoT Edge, por exemplo:

`rtsp://192.168.1.64:554/Streaming/Channels/101/`

> [!TIP]
> Tente visualizar o fluxo da câmara no computador IoT Edge utilizando um leitor de mídia como o VLC.

## <a name="next-steps"></a>Passos seguintes

Já implementou o tempo de funcionaamento do IoT Edge e os módulos LVA para o dispositivo intel NUC gateway.

Para gerir as câmaras, siga o próximo tutorial:

> [!div class="nextstepaction"]
> [Monitorize e gere uma aplicação de deteção de vídeo - objeto e deteção de movimentos](./tutorial-video-analytics-manage.md)