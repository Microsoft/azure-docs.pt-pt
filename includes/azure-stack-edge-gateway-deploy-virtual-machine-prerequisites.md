---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/03/2020
ms.author: alkohli
ms.openlocfilehash: 7b98d3c1febd68a7ee73cf3064f4d8e108ea81fa
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89084889"
---
Antes de poder implantar VMs no seu dispositivo Azure Stack Edge, tem de configurar o seu cliente para se ligar ao dispositivo através do Azure Resource Manager sobre a Azure PowerShell. Para etapas detalhadas, aceda ao [Connect to Azure Resource Manager no seu dispositivo Azure Stack Edge](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).


Certifique-se de que os seguintes passos podem ser utilizados para aceder ao dispositivo a partir do seu cliente (Então fez esta configuração ao ligar-se ao Azure Resource Manager, está apenas a verificar se a configuração foi bem sucedida.): 

1. Verifique se a comunicação do Gestor de Recursos Azure está a funcionar. Escreva:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>:30005/
    ```

1. Ligue para apis de dispositivo local para autenticar. Escreva: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Forneça o nome de utilizador - *EdgeARMuser* e a palavra-passe para ligar através do Azure Resource Manager.

1. Se configurar **o Compute** para Kubernetes, pode saltar este passo. Proceda a garantir que ativou uma interface de rede para calcular. Na UI local, vá às definições **de Compute.** Selecione a interface de rede que utilizará para criar um interruptor virtual. Os VMs que criar serão ligados a um interruptor virtual ligado a esta porta e à rede associada. Certifique-se de que escolhe uma rede que corresponda ao endereço IP estático que irá utilizar para o VM.  

    ![Ativar as definições de cálculo 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    Ativar o cálculo na interface de rede. O Azure Stack Edge criará e gerirá um interruptor virtual correspondente a essa interface de rede. Não introduza IPs específicos para Kubernetes neste momento. Pode levar vários minutos para ativar o cálculo.

    <!--If you decide to use another network interface for compute, make sure that you:
    
    - Delete all the VMs that you have deployed using Azure Resource Manager.
    
    - Delete all virtual network interfaces and the virtual network associated with this network interface. 
    
    - You can now enable another network interface for compute.-->

<!--1. You may also need to configure TLS 1.2 on your client machine if running older versions of AzCopy.--> 

