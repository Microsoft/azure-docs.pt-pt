---
title: Implemente o módulo GPU no seu dispositivo Microsoft Azure Stack Edge Pro a partir do Azure Marketplace| Microsoft Docs
description: Descreve como implementar o módulo IoT ativado pela GPU no seu dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: e22014380d568b12e1e3bec751a75180d0760ab7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568428"
---
# <a name="deploy-a-gpu-enabled-iot-module-from-azure-marketplace-on-azure-stack-edge-pro-gpu-device"></a>Implementar um módulo IoT ativado pela GPU a partir do Azure Marketplace no dispositivo GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Este artigo descreve como implementar um módulo IoT Edge ativado por IoT Edge do Azure Marketplace no seu dispositivo Azure Stack Edge Pro. 

Neste artigo, vai aprender a:
  - Prepare o Azure Stack Edge Pro para executar um módulo GPU.
  - Descarregue e implemente o módulo IoT ativado pela GPU a partir do Azure Marketplace.
  - Monitorize a saída do módulo.

## <a name="about-sample-module"></a>Sobre o módulo de amostra

O módulo de amostra gpu neste artigo inclui pyTorch e tensorFlow código de amostra de benchmarking para CPU contra GPU.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem:

- Você tem acesso a um dispositivo GPU ativado Azure Stack Edge de 1 nó. Este dispositivo está ativado com um recurso no Azure. 
- Configuraste o cálculo deste dispositivo. Siga os passos em [Tutorial: Conigure compute no seu dispositivo Azure Stack Edge](azure-stack-edge-gpu-deploy-configure-compute.md).
- Os seguintes recursos de desenvolvimento num cliente Windows:
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Extensão Azure IoT Edge para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).   


## <a name="get-module-from-azure-marketplace"></a>Obtenha módulo do Azure Marketplace

1. Navegue em todas as [aplicações no Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps)

    ![Navegue em aplicativos no Azure Marketplace](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/browse-apps-marketplace-1.png)

2. Pesquisa por **Começar com GPUs**.

    ![Módulo de amostra de GPU de pesquisa](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/search-gpu-sample-module-1.png)

3. **Selecione Get-lo agora**.

    ![Obtenha módulo de amostra](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/get-sample-module-1.png)

4. **Selecione Continue** a reconhecer os termos de utilização e política de privacidade do fornecedor. 

    ![Obtenha módulo de amostra 2](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/terms-of-use-1.png)

5. Escolha a subscrição que usou para implementar o seu dispositivo Azure Stack Edge Pro.

    ![Selecionar subscrição](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/select-subscription-1.png)

6. Insira o nome do serviço IoT Hub que criou quando configurar o seu dispositivo Azure Stack Edge Pro. Para encontrar este nome de serviço IoT Hub, aceda ao recurso Azure Stack Edge associado ao seu dispositivo no portal Azure. 

    1. Nas opções do menu do painel esquerdo, vá aos **serviços Edge > IoT Edge**. 

        ![Ver compute config](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/view-config-1.png)

    1. Ir para **propriedades**. 

        1. Tome nota do serviço IoT Hub que foi criado quando configurar o cálculo no seu dispositivo Azure Stack Edge Pro.
        2. Note o nome do dispositivo IoT Edge que foi criado quando configurava o cálculo. Usará este nome no passo seguinte.

        ![Configuração de computação de borda](media/azure-stack-edge-gpu-deploy-sample-module/view-compute-config-1.png)

10. Escolha **implementar para um dispositivo**.

11. Introduza o nome do dispositivo IoT Edge ou selecione **Find Device** para navegar entre os dispositivos registados no hub.

    ![Localizar dispositivo](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/find-device-1.png)

12. Selecione **Criar** para continuar o processo padrão de configurar um manifesto de implantação, incluindo adicionar outros módulos se desejar. Os detalhes para o novo módulo, como o URI de imagem, criar opções e propriedades desejadas são predefinidos, mas podem ser alterados.

    ![Selecione criar](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/target-devices-iot-edge-module-1.png)


13. Verifique se o módulo está implantado no seu Hub IoT no portal Azure. Selecione o seu dispositivo, selecione **set Modules** e o módulo deve ser listado na secção **módulos IoT Edge Modules.**

    ![Selecione criar 2](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/running-module-iotres-1.png)

## <a name="monitor-the-module"></a>Monitorize o módulo  

1. Na paleta de comandos do VS Code, execute o **Hub IoT do Azure: Selecionar Hub IoT**.

2. Escolha a subscrição e o hub IoT que contém o dispositivo do IoT Edge que pretende configurar. Neste caso, selecione a subscrição utilizada para implementar o dispositivo Azure Stack Edge Pro e selecione o dispositivo IoT Edge criado para o seu dispositivo Azure Stack Edge Pro. Isto ocorre quando configura o cálculo através do portal Azure nos passos anteriores.

3. No explorador de código VS, expanda a secção Azure IoT Hub. Em **Dispositivos**, deve ver o dispositivo IoT Edge correspondente ao seu dispositivo Azure Stack Edge Pro. 

    1. Selecione este dispositivo, clique à direita e selecione **Start Monitoring Built-in Event Endpoint**.
  
        ![Iniciar a monitorização](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. Vá a **Dispositivos > Módulos** e deverá ver o seu **módulo GPU** a funcionar.

    3. O terminal vs Code também deve mostrar os eventos IoT Hub como a saída de monitorização do seu dispositivo Azure Stack Edge Pro.

        ![Produção de monitorização](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        Pode ver que o tempo necessário para executar o mesmo conjunto de operações (5000 iterações de transformação de forma) por GPU é muito menor do que é para a CPU.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre como configurar a [GPU para utilizar um módulo.](./azure-stack-edge-gpu-configure-gpu-modules.md)