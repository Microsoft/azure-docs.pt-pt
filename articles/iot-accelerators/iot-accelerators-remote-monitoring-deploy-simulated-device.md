---
title: IoT implementar dispositivos simulados personalizados - Azure / Microsoft Docs
description: Este guia de como fazer mostra-lhe como implantar dispositivos simulados personalizados no acelerador de solução de monitorização remota.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/15/2018
ms.topic: conceptual
ms.openlocfilehash: 7cbab38db859935c9f4490d79a131d6c9a7e302b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "66427577"
---
# <a name="deploy-a-new-simulated-device"></a>Implementar um novo dispositivo simulado

Os aceleradores de solução de monitorização remota e simulação de dispositivos permitem definir os seus próprios dispositivos simulados. Este artigo mostra-lhe como implantar um tipo de dispositivo de refrigeração personalizado e um novo tipo de dispositivo de lâmpada para o acelerador de solução de monitorização remota.

Os passos deste artigo assumem que completou o [Create e testa um novo dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md) como orientar e tem os ficheiros que definem o refrigerador personalizado e novos tipos de dispositivos de lâmpada.

Os passos neste guia de como guiar mostram-lhe como:

1. Utilize o SSH para aceder ao sistema de ficheiros da máquina virtual que acolhe o acelerador de solução de monitorização remota.

1. Configure o Docker para carregar os modelos do dispositivo a partir de um local fora do contentor Docker.

1. Executar o acelerador de solução de monitorização remota utilizando ficheiros de modelos de dispositivos personalizados.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Para completar os passos neste guia de como fazer, precisa de uma subscrição ativa do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar este guia, precisa:

- Uma instância implantada do [acelerador de solução de monitorização remota](https://www.azureiotsolutions.com/Accelerators#solutions/types/RM2).
- Uma **carapaça** local para executar os `ssh` `scp` comandos e comandos. No Windows, uma forma fácil de instalar **bash** é instalar [git](https://git-scm.com/download/win).
- Os ficheiros de modelos do seu dispositivo personalizado, como os descritos no [Create e testar um novo dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md).

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Configure Docker

Nesta secção, configurar o Docker para carregar os ficheiros dos modelos do dispositivo a partir da pasta **/tmp/devicemodels** na máquina virtual e não no interior do contentor Docker. Coloque os comandos nesta secção numa **casca de choque** na sua máquina local:

Nesta secção, configurar o Docker para carregar os ficheiros dos modelos do dispositivo a partir da pasta **/tmp/devicemodels** na máquina virtual e não no interior do contentor Docker. Coloque os comandos nesta secção numa **casca de choque** na sua máquina local:

1. Utilize o SSH para ligar à máquina virtual em Azure a partir da sua máquina local. O seguinte comando pressupõe que o endereço IP público da máquina virtual **vm-vikxv** é **104.41.128.108** -- substitua este valor pelo endereço IP público da sua máquina virtual da secção anterior:

   ```sh
    ssh azureuser@104.41.128.108
    ```

    Siga as instruções para iniciar sôm na máquina virtual com a palavra-passe que definiu na secção anterior.

1. Configure o serviço de simulação do dispositivo para carregar os modelos do dispositivo do lado de fora do recipiente. Primeiro abra o ficheiro de configuração do Docker:

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    Localize as definições para o recipiente **de identificação** do dispositivo e edite a definição de **volumes** como mostrado no seguinte corte:

    ```yml
    devicesimulation:
      image: azureiotpcs/device-simulation-dotnet:1.0.0
      networks:
        - default_net
      depends_on:
        - storageadapter
      environment:
        - PCS_KEYVAULT_NAME
        - PCS_AAD_APPID
        - PCS_AAD_APPSECRET
      # How one could mount custom device models
      volumes:
        - /tmp/devicemodels:/app/webservice/data/devicemodels:ro
    ```

    Guarde as alterações.

1. Copie os ficheiros de modelos do dispositivo existentes do recipiente para o novo local. Primeiro, encontre a identificação do recipiente para o recipiente de simulação do dispositivo:

    ```sh
    sudo docker ps
    ```

    Em seguida, copie os ficheiros do modelo do dispositivo para a pasta **tmp** na máquina virtual. O seguinte comando pressupõe que o ID do contentor é c378d6878407 -- substitua este valor pelo ID do seu contentor de simulação do dispositivo:

    ```sh
    sudo docker cp c378d6878407:/app/webservice/data/devicemodels /tmp
    sudo chown -R azureuser /tmp/devicemodels/
    ```

    Mantenha a janela da **festa** com a sessão SSH aberta.

1. Copie os ficheiros de modelos do seu dispositivo personalizado na máquina virtual. Executar este comando em outra casca **de pancada** na máquina onde criou os seus modelos de dispositivos personalizados. Primeiro, navegue para a pasta local que contém os ficheiros JSON do seu modelo de dispositivo. Os seguintes comandos assumem que o endereço IP público da máquina virtual é **104.41.128.108** -- substitua este valor pelo endereço IP público da sua máquina virtual. Introduza a palavra-passe da máquina virtual quando solicitado:

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. Reinicie a simulação do dispositivo O recipiente Docker para utilizar os novos modelos do dispositivo. Executar os seguintes comandos na casca de **bash** com a sessão SSH aberta para a máquina virtual:

    ```sh
    sudo /app/start.sh
    ```

    Se pretender ver o estado dos recipientes estivadores em funcionamento e as suas identificações de contentores, utilize o seguinte comando:

    ```sh
    sudo docker ps
    ```

    Se pretender ver o registo do recipiente de simulação do dispositivo, execute o seguinte comando. Substitua o ID do recipiente pelo ID do seu recipiente de simulação do dispositivo:

    ```sh
    sudo docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>Simulação de execução

Agora pode utilizar os seus modelos de dispositivos personalizados na solução de Monitorização Remota:

1. Lance o seu painel de monitorização remota a partir dos aceleradores de [solução IoT do Microsoft Azure](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Utilize a página **dispositivos** para adicionar dispositivos simulados. Quando adiciona um novo dispositivo simulado, os seus novos modelos de dispositivo estão disponíveis para escolher.

1. Pode utilizar o painel de instrumentos para visualizar os métodos de telemetria e dispositivo de chamada do dispositivo.

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretende explorar mais, deixe o acelerador de solução de Monitorização Remota implantado.

Se já não precisar do acelerador de solução, elimine-o da página [de soluções Provisionadas,](https://www.azureiotsolutions.com/Accelerators#dashboard) selecionando-a e clicando em **Eliminar Solution**.

## <a name="next-steps"></a>Passos seguintes

Este guia mostrou-lhe como implementar modelos de dispositivos personalizados para o acelerador de solução de monitorização remota. O próximo passo sugerido é aprender a [ligar um dispositivo real à sua solução de Monitorização Remota](iot-accelerators-connecting-devices-node.md).
