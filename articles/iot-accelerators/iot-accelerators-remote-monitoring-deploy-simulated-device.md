---
title: IoT implementa dispositivos simulados personalizados - Azure / Microsoft Docs
description: Este guia de como fazer mostra como implementar dispositivos simulados personalizados para o acelerador de soluções de monitorização remota.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/15/2018
ms.topic: conceptual
ms.openlocfilehash: 7cbab38db859935c9f4490d79a131d6c9a7e302b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "66427577"
---
# <a name="deploy-a-new-simulated-device"></a>Implementar um novo dispositivo simulado

Os aceleradores de solução de monitorização remota e simulação do dispositivo permitem-lhe definir os seus próprios dispositivos simulados. Este artigo mostra-lhe como implementar um tipo de dispositivo de refrigeração personalizado e um novo tipo de dispositivo de lâmpada para o acelerador de solução de monitorização remota.

Os passos neste artigo assumem que completou o [Create e testa um novo dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md) como guiar e tem os ficheiros que definem o refrigerador personalizado e os novos tipos de dispositivos de lâmpada.

Os passos neste guia de como guiar mostram-lhe como:

1. Utilize o SSH para aceder ao sistema de ficheiros da máquina virtual que acolhe o acelerador de solução de monitorização remota.

1. Configure o Docker para carregar os modelos do dispositivo a partir de um local fora do contentor Docker.

1. Executar o acelerador de soluções de monitorização remota utilizando ficheiros de modelos de dispositivopersonalizados.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Para completar os passos neste guia de como guiar, precisa de uma subscrição azure ativa.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia como fazer, precisa de:

- Uma instância implantada do acelerador de solução de [monitorização remota](https://www.azureiotsolutions.com/Accelerators#solutions/types/RM2).
- Uma **concha** local para `ssh` `scp` executar e comanda. No Windows, uma maneira fácil de instalar **a bash** é instalar [git](https://git-scm.com/download/win).
- Os ficheiros do modelo do seu dispositivo personalizado, como os descritos no [Create e testam um novo dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md).

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Configure Docker

Nesta secção, configura o Docker para carregar os ficheiros do modelo do dispositivo a partir da pasta **/tmp/devicemodels** na máquina virtual e não no interior do recipiente Docker. Execute os comandos nesta secção **numa** concha na sua máquina local:

Nesta secção, configura o Docker para carregar os ficheiros do modelo do dispositivo a partir da pasta **/tmp/devicemodels** na máquina virtual e não no interior do recipiente Docker. Execute os comandos nesta secção **numa** concha na sua máquina local:

1. Utilize o SSH para ligar à máquina virtual em Azure a partir da sua máquina local. O seguinte comando pressupõe que o endereço IP público da máquina virtual **vm-vikxv** é **104.41.128.108** -- substitua este valor pelo endereço IP público da sua máquina virtual da secção anterior:

   ```sh
    ssh azureuser@104.41.128.108
    ```

    Siga as instruções para iniciar sessão na máquina virtual com a palavra-passe definida na secção anterior.

1. Configure o serviço de simulação do dispositivo para carregar os modelos do dispositivo de fora do recipiente. Primeiro abra o ficheiro de configuração do Docker:

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    Localize as definições do recipiente de **simulação** do dispositivo e edite a definição de **volumes,** tal como mostrado no seguinte corte:

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

1. Copie os ficheiros do modelo do dispositivo existentes do recipiente para o novo local. Em primeiro lugar, encontre o ID do recipiente para o recipiente de simulação do dispositivo:

    ```sh
    sudo docker ps
    ```

    Em seguida, copie os ficheiros do modelo do dispositivo para a pasta **TMP** na máquina virtual. O seguinte comando pressupõe que o ID do recipiente é c378d6878407 -- substitua este valor com o id do recipiente de simulação do dispositivo:

    ```sh
    sudo docker cp c378d6878407:/app/webservice/data/devicemodels /tmp
    sudo chown -R azureuser /tmp/devicemodels/
    ```

    Mantenha a janela da **festa** com a sua sessão ssh aberta.

1. Copie os ficheiros do modelo do seu dispositivo personalizado para a máquina virtual. Execute este comando em outra concha de **bash** na máquina onde criou os seus modelos de dispositivopersonalizados. Em primeiro lugar, navegue para a pasta local que contém os ficheiros JSON do modelo do seu dispositivo. Os seguintes comandos assumem que o endereço IP público da máquina virtual é **104.41.128.108** -- substitua este valor pelo endereço IP público da sua máquina virtual. Introduza a sua senha de máquina virtual quando solicitada:

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. Reiniciar o recipiente de simulação do dispositivo Docker para utilizar os novos modelos do dispositivo. Executar os seguintes comandos na concha da **bash** com a sessão SSH aberta para a máquina virtual:

    ```sh
    sudo /app/start.sh
    ```

    Se quiser ver o estado dos contentores do Docker em funcionamento e das suas identificações de contentores, utilize o seguinte comando:

    ```sh
    sudo docker ps
    ```

    Se quiser ver o registo do recipiente de simulação do dispositivo, execute o seguinte comando. Substitua o ID do recipiente pela identificação do seu recipiente de simulação do dispositivo:

    ```sh
    sudo docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>Executar simulação

Pode agora utilizar os seus modelos de dispositivos personalizados na solução de Monitorização Remota:

1. Lance o seu painel de monitorização remota a partir dos aceleradores de [soluções Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Utilize a página **Dispositivos** para adicionar dispositivos simulados. Quando adiciona um novo dispositivo simulado, os seus novos modelos de dispositivo saem disponíveis para escolher.

1. Pode utilizar o painel de instrumentos para visualizar os métodos de telemetria do dispositivo e chamar.

## <a name="clean-up-resources"></a>Limpar recursos

Se planeia explorar mais, deixe o acelerador de solução de monitorização remota implantado.

Se já não necessitar do acelerador de solução, elimine-o da página de [soluções Provisionadas,](https://www.azureiotsolutions.com/Accelerators#dashboard) selecionando-a e clicando em **Eliminar a Solução**.

## <a name="next-steps"></a>Passos seguintes

Este guia mostrou-lhe como implementar modelos de dispositivos personalizados para o acelerador de soluções de monitorização remota. O próximo passo sugerido é aprender a [ligar um dispositivo real à sua solução](iot-accelerators-connecting-devices-node.md)de Monitorização Remota .
