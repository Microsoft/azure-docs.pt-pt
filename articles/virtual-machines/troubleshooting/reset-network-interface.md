---
title: Como redefinir a interface de rede para a VM do Windows do Azure | Microsoft Docs
description: Mostra como redefinir a interface de rede para a VM do Windows do Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/16/2018
ms.author: genli
ms.openlocfilehash: 1c49c6221e9b310a1b14a4e06a296befc7f6da4d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111717"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Como redefinir a interface de rede para a VM do Windows do Azure 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Este artigo mostra como redefinir o adaptador de rede para a VM do Windows do Azure para resolver problemas quando você não pode se conectar ao Microsoft Azure VM (máquina virtual) do Windows após:

* Você desabilita a NIC (interface de rede) padrão. 
* Você define manualmente um IP estático para a NIC. 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>Repor a interface de rede

### <a name="for-vms-deployed-in-resource-group-model"></a>Para VMs implantadas no modelo de grupo de recursos

1.  Aceda ao [Portal do Azure](https://ms.portal.azure.com).
2.  Selecione a máquina virtual afetada.
3.  Selecione **rede** e, em seguida, selecione a interface de rede da VM.

    ![Local da interface de rede](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  Selecione **configurações de IP**.
5.  Selecione o IP. 
6.  Se a **atribuição de IP privado** não for **estática**, altere-a para **estático**.
7.  Altere o **endereço IP** para outro endereço IP que está disponível na sub-rede.
8. A máquina virtual será reiniciada para inicializar a nova NIC para o sistema.
9.  Tente usar o RDP em seu computador. Se for bem-sucedido, você poderá alterar o endereço IP privado de volta para o original, se desejar. Caso contrário, você pode mantê-lo. 

#### <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

1. Verifique se você tem [as Azure PowerShell mais recentes](https://docs.microsoft.com/powershell/azure/overview) instaladas
2. Abra uma sessão de Azure PowerShell com privilégios elevados (executar como administrador). Execute os seguintes comandos:

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
    Get-AzVM -ResourceGroupName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzVM
    ```
3. Tente usar o RDP em seu computador.  Se for bem-sucedido, você poderá alterar o endereço IP privado de volta para o original, se desejar. Caso contrário, você pode mantê-lo.

### <a name="for-classic-vms"></a>Para VMs clássicas

Para redefinir a interface de rede, siga estas etapas:

#### <a name="use-azure-portal"></a>Utilizar o portal do Azure

1.  Aceda ao [Portal do Azure]( https://ms.portal.azure.com).
2.  Selecione **máquinas virtuais (clássicas)** .
3.  Selecione a máquina virtual afetada.
4.  Selecione **endereços IP**.
5.  Se a **atribuição de IP privado** não for **estática**, altere-a para **estático**.
6.  Altere o **endereço IP** para outro endereço IP que está disponível na sub-rede.
7.  Selecione **Guardar**.
8.  A máquina virtual será reiniciada para inicializar a nova NIC para o sistema.
9.  Tente usar o RDP em seu computador. Se for bem-sucedido, você poderá optar por reverter o endereço IP privado de volta para o original.  

#### <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

1. Verifique se você tem [as Azure PowerShell mais recentes](https://docs.microsoft.com/powershell/azure/overview) instaladas.
2. Abra uma sessão de Azure PowerShell com privilégios elevados (executar como administrador). Execute os seguintes comandos:

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
    Get-AzureVM -ResourceGroupName $CloudService -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP |Update-AzureVM
    ```
3. Tente usar o RDP em seu computador. Se for bem-sucedido, você poderá alterar o endereço IP privado de volta para o original, se desejar. Caso contrário, você pode mantê-lo. 

## <a name="delete-the-unavailable-nics"></a>Excluir as NICs não disponíveis
Depois de fazer a área de trabalho remota para o computador, você deve excluir as NICs antigas para evitar o possível problema:

1.  Abra Device Manager.
2.  Selecione **exibir** > **Mostrar dispositivos ocultos**.
3.  Selecione **adaptadores de rede**. 
4.  Verifique os adaptadores nomeados como "adaptador de rede Microsoft Hyper-V".
5.  Você pode ver um adaptador indisponível que está esmaecido. Clique com o botão direito do mouse no adaptador e selecione Desinstalar.

    ![a imagem da NIC](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Desinstale apenas os adaptadores indisponíveis que têm o nome "adaptador de rede Microsoft Hyper-V". Se você desinstalar qualquer um dos outros adaptadores ocultos, isso poderá causar problemas adicionais.
    >
    >

6.  Agora, todos os adaptadores indisponíveis devem ser apagados do seu sistema.
