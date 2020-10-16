---
title: Tutorial para filtrar, analisar dados com computação no Azure Stack Edge Pro GPU Microsoft Docs
description: Aprenda a configurar o papel de computação no GPU Azure Stack Edge Pro e use-o para transformar dados antes de enviar para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 711da24b3edf08f4867109d0d70165955236c39a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743425"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-pro-gpu-device"></a>Tutorial: Configurar computação no dispositivo GPU Azure Stack Edge Pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Este tutorial descreve como configurar um papel de computação e criar um cluster Kubernetes no seu dispositivo Azure Stack Edge Pro. 

Este procedimento pode demorar cerca de 20 a 30 minutos para ser concluído.


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure computação
> * Obter pontos finais de Kubernetes

 
## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar um papel de computação no seu dispositivo Azure Stack Edge Pro, certifique-se de que:

- Ativou o seu dispositivo Azure Stack Edge Pro, conforme descrito no [Activate Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).
- Certifique-se de que seguiu as instruções na [rede de cálculo Ativa](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) e:
    - Permitiu uma interface de rede para o cálculo.
    - IPs de nó de kubernetes atribuídos e IPs de serviço externo Kubernetes.

## <a name="configure-compute"></a>Configure computação

Para configurar o cálculo no seu Azure Stack Edge Pro, irá criar um recurso IoT Hub através do portal Azure.

1. No portal Azure do seu recurso Azure Stack Edge, vá ao **Overview**. No painel direito, no azulejo **compute,** **selecione Começar**.

    ![Começar com o computo](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-1.png)

2. No azulejo **computacional Configure Edge,** selecione **Configure compute**.

    ![Configure computação](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-2.png)

3. Na lâmina de **computação Configure Edge,** insira o seguinte:

   
    |Campo  |Valor  |
    |---------|---------|
    |IoT Hub     | Escolha entre **Novo** ou **Já.** <br> Por predefinição, é utilizado um escalão Standard (S1) para criar um recurso IoT. Para utilizar um recurso IoT de escalão gratuito, crie um e, em seguida, selecione o recurso existente. <br> Em cada caso, o recurso IoT Hub utiliza o mesmo grupo de subscrição e recursos que é utilizado pelo recurso Azure Stack Edge.     |
    |Nome     |Insira um nome para o seu recurso IoT Hub.         |

    ![Começar com o compute 2](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-3.png)

4. Selecione **Criar**. A criação de recursos IoT Hub demora vários minutos. Após a criação do recurso IoT Hub, as atualizações de azulejos **de computação Configure** para mostrar a configuração do cálculo. 

    ![Começar com o computo 3](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-4.png)

5. Para confirmar que a função de computação Edge foi configurada, selecione **Ver Computação** no azulejo **do cálculo Configure.**
    
    ![Começar com o compute 4](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-5.png)

    > [!NOTE]
    > Se o diálogo **Configure Compute** for fechado antes de o IoT Hub estar associado ao dispositivo Azure Stack Edge Pro, o Hub IoT é criado mas não é mostrado na configuração do cálculo. 
    
Quando a função de computação Edge é configurada no dispositivo Edge, cria dois dispositivos: um dispositivo IoT e um dispositivo IoT Edge. Ambos os dispositivos podem ser vistos no recurso IoT Hub. Um IoT Edge Runtime também está a ser executado neste dispositivo IoT Edge. Neste momento, apenas a plataforma Linux está disponível para o seu dispositivo IoT Edge.

Pode levar 20 a 30 minutos para configurar o cálculo, uma vez que nos bastidores, máquinas virtuais e cluster Kubernetes estão sendo criados. 

Depois de configurar com sucesso o computação no portal Azure, existe um cluster Kubernetes e um utilizador padrão associado ao espaço de nome IoT (um espaço de nome do sistema controlado por Azure Stack Edge Pro). 

## <a name="get-kubernetes-endpoints"></a>Obter pontos finais de Kubernetes

Para configurar um cliente para aceder ao cluster Kubernetes, você precisará do ponto final de Kubernetes. Siga estes passos para obter o ponto final da API da Kubernetes a partir da UI local do seu dispositivo Azure Stack Edge Pro.

1. Na uI web local do seu dispositivo, aceda à página **dispositivos.**
2. Sob os **pontos finais**do Dispositivo, copie o ponto final de **serviço da API da Kubernetes.** Este ponto final é uma cadeia no seguinte formato: `https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]` . 

    ![Página do dispositivo na UI local](./media/azure-stack-edge-j-series-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

3. Salve a corda do ponto final. Você usará isto mais tarde ao configurar um cliente para aceder ao cluster Kubernetes via kubectl.

4. Enquanto estiver na internet local, pode:

    - Vá à API de Kubernetes, selecione **definições avançadas** e descarregue um ficheiro de configuração avançado para Kubernetes. 

        ![Página do dispositivo no UI local 1](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-1.png)

        Se lhe for fornecida uma chave da Microsoft (os utilizadores selecionados podem tê-lo), então pode utilizar este ficheiro config.

        ![Página do dispositivo no UI local 2](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-2.png)

    - Também pode ir ao ponto final **do painel de instrumentos de Kubernetes** e descarregar um `aseuser` ficheiro config. 
    
        ![Página do dispositivo no UI local 3](./media/azure-stack-edge-gpu-deploy-configure-compute/download-aseuser-config-1.png)

        Pode utilizar este ficheiro config para iniciar sedições no painel de instrumentos de Kubernetes ou depurar quaisquer problemas no seu cluster Kubernetes. Para obter mais informações, consulte [o painel access Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard). 


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Configure computação
> * Obter pontos finais de Kubernetes


Para aprender a administrar o seu dispositivo Azure Stack Edge Pro, consulte:

> [!div class="nextstepaction"]
> [Use uI web local para administrar um Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)
