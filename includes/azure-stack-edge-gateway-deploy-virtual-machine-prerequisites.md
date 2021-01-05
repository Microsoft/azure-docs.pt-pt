---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/21/2020
ms.author: alkohli
ms.openlocfilehash: f2443765ecc9116193cefbc729ced25fa5657e59
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763438"
---
Antes de poder implantar VMs no seu dispositivo Azure Stack Edge, tem de configurar o seu cliente para se ligar ao dispositivo através do Azure Resource Manager sobre a Azure PowerShell. Para etapas detalhadas, aceda ao [Connect to Azure Resource Manager no seu dispositivo Azure Stack Edge](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).


Certifique-se de que os seguintes passos podem ser utilizados para aceder ao dispositivo a partir do seu cliente (Então fez esta configuração ao ligar-se ao Azure Resource Manager, está apenas a verificar se a configuração foi bem sucedida.): 

1. Verifique se a comunicação do Gestor de Recursos Azure está a funcionar. Escreva:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Ligue para apis de dispositivo local para autenticar. Escreva: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Forneça o nome de utilizador - *EdgeARMuser* e a palavra-passe para ligar através do Azure Resource Manager.

1. Se configurar **o Compute** para Kubernetes, pode saltar este passo. Proceda a garantir que ativou uma interface de rede para calcular. Na UI local, vá às definições **de Compute.** Selecione a interface de rede que utilizará para criar um interruptor virtual. Os VMs que criar serão ligados a um interruptor virtual ligado a esta porta e à rede associada. Certifique-se de escolher uma rede que corresponda ao endereço IP que irá utilizar para o VM.  

    ![Ativar as definições de cálculo 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    Ativar o cálculo na interface de rede. O Azure Stack Edge criará e gerirá um interruptor virtual correspondente a essa interface de rede. Não introduza IPs específicos para Kubernetes neste momento. Pode levar vários minutos para ativar o cálculo.

    > [!NOTE]
    > Se criar VMs GPU, selecione uma interface de rede ligada à Internet. Isto permite-lhe instalar a extensão GPU no seu dispositivo.


1. Ativar o papel de VM a partir do portal Azure. Este passo cria uma subscrição única para o seu dispositivo que é usada para criar VMs através das APIs locais do dispositivo. 

    1. Para ativar o papel de VM, no portal Azure, aceda ao recurso Azure Stack Edge para o seu dispositivo Azure Stack Edge. Vá ao **Edge compute > Máquinas Virtuais**.

        ![Adicionar imagem VM 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

    1. Selecione **Máquinas Virtuais** para ir à página **'Vista Geral'.** **Ativar a** gestão virtual da nuvem de máquina.
        ![Adicionar imagem VM 2](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)