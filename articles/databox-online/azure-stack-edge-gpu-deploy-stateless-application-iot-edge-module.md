---
title: Implementar a aplicação apátrida da Kubernetes no GPU Azure Stack Edge Pro através do módulo IoT Edge| Microsoft Docs
description: Descreve como implementar uma aplicação apátrida Kubernetes no seu dispositivo GPU Azure Stack Edge Pro utilizando um módulo IoT Edge que é acedido através de um IP externo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 4eda6eaf69fad6f8600651660ae4ac6223fe8f8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438068"
---
# <a name="use-iot-edge-module-to-run-a-kubernetes-stateless-application-on-your-azure-stack-edge-pro-gpu-device"></a>Utilize o módulo IoT Edge para executar uma aplicação apátrida kubernetes no seu dispositivo GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como pode utilizar um módulo IoT Edge para implementar uma aplicação apátrida no seu dispositivo Azure Stack Edge Pro.

Para implementar a aplicação apátrida, você tomará os seguintes passos:

- Certifique-se de que os pré-requisitos estão completos antes de colocar um módulo IoT Edge.
- Adicione um módulo IoT Edge para aceder à rede de computação no seu Azure Stack Edge Pro.
- Verifique se o módulo pode aceder à interface de rede ativada.

Neste artigo de como fazer, você usará um módulo de aplicação webserver para demonstrar o cenário.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, vai precisar de:

- Um dispositivo Azure Stack Edge Pro. Certifique-se de que:

    - As definições da rede computacional estão configuradas no dispositivo.
    - O dispositivo é ativado de acordo com os passos do [Tutorial: Ative o seu dispositivo](azure-stack-edge-gpu-deploy-activate.md).
- Completou o passo **de computação Configure** de acordo com o [Tutorial: Conigure computo no seu dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-compute.md) no seu dispositivo. O seu dispositivo deve ter um recurso IoT Hub associado, um dispositivo IoT e um dispositivo IoT Edge.


## <a name="add-webserver-app-module"></a>Adicionar módulo de aplicativo webserver

Tome os seguintes passos para adicionar um módulo de aplicação webserver no seu dispositivo Azure Stack Edge Pro.

1. No recurso IoT Hub associado ao seu dispositivo, aceda à **Gestão Automática de Dispositivos > IoT Edge**.
1. Selecione e clique no dispositivo IoT Edge associado ao seu dispositivo Azure Stack Edge Pro. 

    ![Selecione dispositivo IoT Edge](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-device-1.png)  

1. Selecione **Definir módulos**. Nos **módulos definidos no dispositivo**, selecione + **Adicione** e, em seguida, selecione o Módulo de **Borda IoT**.

    ![Selecione módulo IoT Edge](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-module-1.png)

1. No **módulo Add IoT Edge:**

    1. Especifique um **nome** para o seu módulo de aplicação webserver que pretende implementar.
    2. No **separador definições do Módulo,** forneça um **URI de imagem** para a imagem do módulo. É recuperado um módulo que corresponda ao nome e etiquetas fornecidos. Neste caso, `nginx:stable` irá retirar uma imagem nginx estável (marcada como estável) do [repositório](https://hub.docker.com/_/nginx/)público do Docker .

        ![Adicionar módulo IoT Edge](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/set-module-settings-1.png)    

    3. No **separador Opções de Criação** de Recipientes, cole o seguinte código de amostra:  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        Esta configuração permite-lhe aceder ao módulo utilizando a rede computacional IP em *http* na porta TCP 8080 (com a porta webserver padrão a ser de 80). Selecione **Adicionar**.

        ![Especifique informações de porta na lâmina do módulo personalizado IoT Edge](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

    4. Selecione **Rever + criar**. Reveja os detalhes do módulo e **selecione Criar**.

## <a name="verify-module-access"></a>Verificar acesso ao módulo

1. Verifique se o módulo está implantado com sucesso e está a funcionar. No **separador Módulos,** o estado de **funcionamento** do módulo deve estar em funcionamento .  

    ![Verifique o estado do módulo em execução](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

1. Para obter o ponto final externo da aplicação webserver, [aceda ao dashboard Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard). 
1. No painel esquerdo do painel de instrumentos, filtra-se por **iotedge** namespace. Vá para **a Discovery and Load balanceing > Services**. De acordo com a lista de serviços listados, localize o ponto final externo para o módulo de aplicação webserver. 

    ![Conecte-se à aplicação webserver no ponto final externo](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/connect-external-endpoint-1.png)

1. Selecione o ponto final externo para abrir uma nova janela do navegador.

    Devias ver que a aplicação webserver está a funcionar.

    ![Verificar ligação ao módulo sobre a porta especificada](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-webserver-app-1.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba como expor a aplicação imponente através de um módulo IoT Edge<!--insert link-->.
