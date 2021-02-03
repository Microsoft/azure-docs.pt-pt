---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: 8f3031669723cd61715c12a42f99869ac0eaf3bc
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99500444"
---
Antes de poder implantar VMs no seu dispositivo Azure Stack Edge, tem de configurar o seu cliente para se ligar ao dispositivo através do Azure Resource Manager sobre a Azure PowerShell. Para etapas detalhadas, aceda ao [Connect to Azure Resource Manager no seu dispositivo Azure Stack Edge](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).

Certifique-se de que podem ser utilizados os seguintes passos para aceder ao dispositivo a partir do seu cliente. (Fez esta configuração ao ligar-se ao Azure Resource Manager. Está agora apenas a verificar se a configuração foi bem sucedida.) 

1. Verifique se a comunicação do Gestor de Recursos Azure está a funcionar. Introduza:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Ligue para apis de dispositivo local para autenticar. Introduza: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Forneça o nome de utilizador - *EdgeARMuser* e a palavra-passe para ligar através do Azure Resource Manager.

1. Se configurar **o Compute** para Kubernetes, pode saltar este passo. Proceda a garantir que ativou uma interface de rede para calcular. Na sua interface de utilizador local, aceda às definições **de Compute.** Selecione a interface de rede que pretende utilizar para criar um interruptor virtual. Os VMs que criar serão ligados a um interruptor virtual ligado a esta porta e à rede associada. Certifique-se de escolher uma rede que corresponda ao endereço IP que irá utilizar para o VM.  

    ![Screenshot que mostra como ativar as definições de computação.](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    Ativar o cálculo na interface de rede. O Azure Stack Edge criará e gerirá um interruptor virtual correspondente a essa interface de rede. Não introduza IPs específicos para Kubernetes neste momento. Pode levar vários minutos para ativar o cálculo.

    > [!NOTE]
    > Se estiver a criar VMs GPU, selecione uma interface de rede ligada à internet. Isto permite-lhe instalar uma extensão GPU no seu dispositivo.


