---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: f166413507afb9aff814eaddaade099d2e34ae68
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554164"
---
Antes de poder implantar VMs no seu dispositivo Azure Stack Edge, tem de configurar o seu cliente para se ligar ao dispositivo através do Azure Resource Manager sobre a Azure PowerShell. Para obter instruções detalhadas, consulte [Connect to Azure Resource Manager no seu dispositivo Azure Stack Edge](../articles/databox-online/azure-stack-edge-gpu-connect-resource-manager.md).

Certifique-se de que pode utilizar os seguintes passos para aceder ao dispositivo a partir do seu cliente. Já fez esta configuração quando ligou ao Azure Resource Manager, e agora está a verificar se a configuração foi bem sucedida. 

1. Verifique se a comunicação do Gestor de Recursos Azure está a funcionar executando o seguinte comando:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Para chamar as APIs do dispositivo local para autenticar, insira: 

    `login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d`

    Para ligar através do Azure Resource Manager, forneça o nome de utilizador *EdgeArmUser* e a sua palavra-passe.

1. Se configurar o cálculo para Kubernetes, pode saltar este passo. Caso contrário, certifique-se de que ativou uma interface de rede para calcular fazendo o seguinte: 

   a. Na sua interface de utilizador local, aceda às definições **de Compute.**  
   b. Selecione a interface de rede que pretende utilizar para criar um interruptor virtual. Os VMs que criar serão ligados a um interruptor virtual ligado a esta porta e à rede associada. Certifique-se de escolher uma rede que corresponda ao endereço IP que irá utilizar para o VM.  

    ![Screenshot do painel de configurações de configuração compute.](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

   c. Em **Ativar para calcular** na interface de rede, selecione **Sim**. O Azure Stack Edge criará e gerirá um interruptor virtual que corresponda a essa interface de rede. Não introduza IPs específicos para Kubernetes neste momento. Pode levar vários minutos para ativar o cálculo.

    > [!NOTE]
    > Se estiver a criar VMs GPU, selecione uma interface de rede que esteja ligada à internet. Ao fazê-lo, permite-lhe instalar uma extensão GPU no seu dispositivo.