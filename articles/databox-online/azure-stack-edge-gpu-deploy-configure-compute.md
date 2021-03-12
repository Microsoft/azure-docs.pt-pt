---
title: Tutorial para filtrar, analisar dados com computação no Azure Stack Edge Pro GPU | Microsoft Docs
description: Aprenda a configurar o papel de computação no GPU Azure Stack Edge Pro e use-o para transformar dados antes de enviar para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 750b7a8367d46434f48626268a0eb37c9edddfb1
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102633546"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-pro-gpu-device"></a>Tutorial: Configurar computação no dispositivo GPU Azure Stack Edge Pro

<!--ALPA WILL VERIFY - [!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

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

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]

## <a name="get-kubernetes-endpoints"></a>Obter pontos finais de Kubernetes

Para configurar um cliente para aceder ao cluster Kubernetes, você precisará do ponto final de Kubernetes. Siga estes passos para obter o ponto final da API da Kubernetes a partir da UI local do seu dispositivo Azure Stack Edge Pro.

1. Na uI web local do seu dispositivo, aceda à página **dispositivos.**
2. Sob os **pontos finais** do Dispositivo, copie o ponto final de **serviço da API da Kubernetes.** Este ponto final é uma cadeia no seguinte formato: `https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]` . 

    ![Página do dispositivo na UI local](./media/azure-stack-edge-gpu-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

3. Salve a corda do ponto final. Utilizará esta cadeia de ponto final mais tarde ao configurar um cliente para aceder ao cluster Kubernetes através de kubectl.

4. Enquanto estiver na internet local, pode:

    - Vá à API de Kubernetes, selecione **definições avançadas** e descarregue um ficheiro de configuração avançado para Kubernetes. 

        ![Página do dispositivo no UI local 1](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-1.png)

        Se lhe for fornecida uma chave da Microsoft (os utilizadores selecionados podem ter uma chave), então pode utilizar este ficheiro config.

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
