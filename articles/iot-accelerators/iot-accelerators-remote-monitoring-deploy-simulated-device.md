---
title: IoT implementar dispositivos simulados personalizados - Azure | Documentos da Microsoft
description: Este guia de procedimentos mostra-lhe como implementar dispositivos simulados personalizados para o acelerador de solução de monitorização remota.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/15/2018
ms.topic: conceptual
ms.openlocfilehash: cdd2b24e6f833ee0acb229a37c7cd3b8b6f36339
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66143410"
---
# <a name="deploy-a-new-simulated-device"></a>Implementar um novo dispositivo simulado

Monitorização remota e a simulação do dispositivo solution accelerators ambas permitem-lhe definir seus próprios dispositivos simulados. Este artigo mostra-lhe como implementar um tipo de dispositivo chiller personalizado e um novo tipo de dispositivo de lâmpada elétrica o acelerador de solução de monitorização remota.

Os passos neste artigo partem do princípio de que concluiu o [criar e teste de um novo dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md) procedimentos orientar e os arquivos que definem as chiller personalizada e novos tipos de dispositivo de lâmpada elétrica.

Os passos neste guia de procedimentos mostram como para:

1. Utilize o SSH para aceder ao sistema de ficheiros da máquina virtual que aloja o acelerador de solução de monitorização remota.

1. Configure Docker para carregar os modelos de dispositivo a partir de uma localização fora do contentor de Docker.

1. Execute o solution accelerator monitorização remota usando arquivos de modelo de dispositivo personalizado.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Para concluir os passos neste guia de procedimentos, precisa de uma subscrição do Azure Active Directory.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de procedimentos, terá de:

- Uma instância implantada do [solution accelerator do monitoramento remoto](https://www.azureiotsolutions.com/Accelerators#solutions/types/RM2).
- Um local **bash** shell a executar o `ssh` e `scp` comandos. No Windows, uma forma fácil de instalar **bash** é instalar [git](https://git-scm.com/download/win).
- Os ficheiros de modelo de dispositivo personalizado, como os que foram descritos na [criar e teste de um novo dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md).

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Configurar Docker

Nesta secção, vai configurar Docker para carregar os ficheiros de modelo do dispositivo do **/tmp/devicemodels** pasta na máquina virtual, em vez no interior do contentor de Docker. Execute os comandos nesta secção num **bash** shell no seu computador local:

Nesta secção, vai configurar Docker para carregar os ficheiros de modelo do dispositivo do **/tmp/devicemodels** pasta na máquina virtual, em vez no interior do contentor de Docker. Execute os comandos nesta secção num **bash** shell no seu computador local:

1. Utilize o SSH para ligar à máquina virtual no Azure a partir do seu computador local. O comando seguinte assume o endereço IP público da máquina virtual **vm vikxv** é **104.41.128.108** – substitua este valor com o endereço IP público da sua máquina virtual da secção anterior:

   ```sh
    ssh azureuser@104.41.128.108
    ```

    Siga as instruções para iniciar sessão na máquina virtual com a palavra-passe definido na secção anterior.

1. Configure o serviço de simulação de dispositivo para carregar os modelos de dispositivo a partir de fora do contentor. Em primeiro lugar, abra o ficheiro de configuração do Docker:

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    Localizar as definições para o **devicesimulation** contentor e editar os **volumes** definição conforme mostrado no seguinte fragmento:

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

1. Copie os ficheiros de modelo de dispositivo existentes do contêiner para a nova localização. Em primeiro lugar, localize o ID de contentor para o contentor de simulação do dispositivo:

    ```sh
    docker ps
    ```

    Em seguida, copie os ficheiros de modelo do dispositivo para o **tmp** pasta na máquina virtual. O comando seguinte assume o ID de contentor é c378d6878407 – substitua este valor com o seu ID de contentor de simulação do dispositivo:

    ```sh
    docker cp c378d6878407:/app/webservice/data/devicemodels /tmp
    ```

    Manter o **bash** janela com a sessão SSH aberta.

1. Copie os ficheiros de modelo de dispositivo personalizado para a máquina virtual. Execute este comando em outro **bash** shell na máquina onde criou os modelos de dispositivo personalizado. Em primeiro lugar, navegue para a pasta local que contém os ficheiros JSON do modelo de dispositivo. Os seguintes comandos partem do princípio de é o endereço IP público da máquina virtual **104.41.128.108** – substitua este valor com o endereço IP público da sua máquina virtual. Introduza a palavra-passe de máquina virtual quando lhe for pedido:

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. Reinicie o contentor de Docker de simulação de dispositivo para utilizar os novos modelos de dispositivo. Execute os seguintes comandos **bash** shell com a abrir sessão SSH à máquina virtual:

    ```sh
    sudo /app/start.sh
    ```

    Se quiser ver o estado dos contentores de Docker em execução e seus IDs de contentor, utilize o seguinte comando:

    ```sh
    docker ps
    ```

    Se pretender ver o registo do contentor de simulação de dispositivo, execute o seguinte comando. Substitua o ID de contentor com o ID do seu contentor de simulação do dispositivo:

    ```sh
    docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>Execução de simulação

Agora, pode utilizar os modelos de dispositivo personalizada na solução de monitorização remota:

1. Iniciar o dashboard de monitorização remota [Aceleradores de soluções do Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Utilize o **dispositivos** página para adicionar dispositivos simulados. Quando adiciona um novo dispositivo simulado, os novos modelos de dispositivo estão disponíveis para escolha.

1. Pode utilizar o dashboard para ver a telemetria do dispositivo e chamar métodos de dispositivo.

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser explorar ainda mais, deixe o solution accelerator monitorização remota implementado.

Se já não precisar do solution accelerator, elimine-o a partir da [aprovisionado de soluções](https://www.azureiotsolutions.com/Accelerators#dashboard) página, selecioná-la e, em seguida, clicando em **Eliminar solução**.

## <a name="next-steps"></a>Passos Seguintes

Este guia de mostrar como implementar modelos de dispositivo personalizado para o acelerador de solução de monitorização remota. A próxima etapa sugerida é saber como [ligar um dispositivo real à sua solução de monitorização remota](iot-accelerators-connecting-devices-node.md).
