---
title: Como repor a interface de rede para VM do Windows do Azure | Documentos da Microsoft
description: Mostra como repor o interface de rede de VM do Windows Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/16/2018
ms.author: genli
ms.openlocfilehash: 3a8e005f8678deef9fc4aebd2d620619fe6074bc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60307334"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Como repor a interface de rede para a VM do Windows Azure 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Este artigo mostra como repor a interface de rede para a VM do Windows Azure resolver problemas quando não é possível ligar ao Microsoft Windows Máquina Virtual (VM) do Azure após:

* Desativar a predefinição de Interface de rede (NIC). 
* Definir manualmente um IP estático para o NIC. 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>Repor a interface de rede

### <a name="for-vms-deployed-in-resource-group-model"></a>Para as VMs implementadas no modelo de grupo de recursos

1.  Aceda ao [Portal do Azure](https://ms.portal.azure.com).
2.  Selecione a Máquina Virtual afetados.
3.  Selecione **redes** e, em seguida, selecione a rede de Interface da VM.

    ![Localização da interface de rede](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  Selecione **configurações de IP**.
5.  Selecione o IP. 
6.  Se o **atribuição de IP privado** není **estático**, altere-o para **estático**.
7.  Alteração da **endereço IP** para outro endereço IP que está disponível na sub-rede.
8. A máquina virtual irá reiniciar para inicializar o NIC novo para o sistema.
9.  Experimente o RDP para o seu computador. Se tiver êxito, pode alterar o endereço IP privado para o original, se desejar. Caso contrário, puderem mantê-lo. 

#### <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

1. Certifique-se de que tenha [o Azure PowerShell mais recente](https://docs.microsoft.com/powershell/azure/overview) instalado
2. Abra uma sessão elevada do PowerShell do Azure (executar como administrador). Execute os seguintes comandos:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $ResourceGroup = "<Resource Group>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzAccount
    Select-AzSubscription -SubscriptionId $SubscriptionId 
    
    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP

    #Add/Change static IP. This process will not change MAC address
    Get-AzVM -ServiceName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzVM
    ```
3. Experimente o RDP para o seu computador.  Se tiver êxito, pode alterar o endereço IP privado para o original, se desejar. Caso contrário, puderem mantê-lo.

### <a name="for-classic-vms"></a>Para VMs clássicas

Para repor a interface de rede, siga estes passos:

#### <a name="use-azure-portal"></a>Utilizar o portal do Azure

1.  Aceda ao [Portal do Azure]( https://ms.portal.azure.com).
2.  Selecione **máquinas virtuais (clássicas)** .
3.  Selecione a Máquina Virtual afetados.
4.  Selecione **endereços IP**.
5.  Se o **atribuição de IP privado** není **estático**, altere-o para **estático**.
6.  Alteração da **endereço IP** para outro endereço IP que está disponível na sub-rede.
7.  Selecione **Guardar**.
8.  A máquina virtual irá reiniciar para inicializar o NIC novo para o sistema.
9.  Experimente o RDP para o seu computador. Se tiver êxito, é possível reverter o endereço IP privado para o original.  

#### <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

1. Certifique-se de que tenha [o Azure PowerShell mais recente](https://docs.microsoft.com/powershell/azure/overview) instalado.
2. Abra uma sessão elevada do PowerShell do Azure (executar como administrador). Execute os seguintes comandos:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $CloudService = "<Cloud Service>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId 

    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP
    
    #Add/Change static IP. This process will not change MAC address
    Get-AzureVM -ServiceName $CloudService -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP |Update-AzureVM
    ```
3. Experimente o RDP para o seu computador. Se tiver êxito, pode alterar o endereço IP privado para o original, se desejar. Caso contrário, puderem mantê-lo. 

## <a name="delete-the-unavailable-nics"></a>Eliminar os NICs indisponíveis
Depois de o poder de ambiente de trabalho remoto à máquina, tem de eliminar os NICs antigos para evitar o potencial problema:

1.  Abra o Gestor de dispositivos.
2.  Selecione **View** > **Mostrar dispositivos ocultos**.
3.  Selecione **adaptadores de rede**. 
4.  Verificar a existência de adaptadores com o nome "Placa de rede do Microsoft Hyper-V".
5.  Poderá ver um adaptador indisponível por estar desativado. O adaptador com o botão direito e, em seguida, selecione Uninstall.

    ![a imagem da NIC](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Desinstale apenas os adaptadores indisponíveis que têm o nome "Placa de rede do Microsoft Hyper-V". Se desinstalar qualquer um dos outros adaptadores ocultos, isso pode causar problemas adicionais.
    >
    >

6.  Agora, todos os adaptadores indisponíveis devem ser limpos do seu sistema.
