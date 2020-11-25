---
title: Como redefinir a interface de rede para O Azure Windows VM Microsoft Docs
description: Mostra como redefinir interface de rede para Azure Windows VM
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
ms.openlocfilehash: 6c4e507df0f112934979d4e59778b667743cf623
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022909"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Como repor a interface de rede da VM do Windows no Azure 

Este artigo mostra como redefinir a interface de rede para O Azure Windows VM para resolver problemas quando não é possível ligar à Microsoft Azure Windows Virtual Machine (VM) depois:

* Desativa a Interface de Rede padrão (NIC). 
* Define manualmente um IP estático para o NIC. 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>Redefinir interface de rede

### <a name="for-vms-deployed-in-resource-group-model"></a>Para VMs implantados no modelo de grupo de recursos

1.  Aceda ao [portal do Azure](https://ms.portal.azure.com).
2.  Selecione a Máquina Virtual afetada.
3.  Selecione **Networking** e, em seguida, selecione a interface de rede do VM.

    ![Localização da interface de rede](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  Selecione **configurações IP**.
5.  Selecione o IP. 
6.  Se a **atribuição de IP privada**  não for  **estática,** altere-a para **Estática**.
7.  Altere o **endereço IP** para outro endereço IP disponível na Sub-rede.
8. A máquina virtual reiniciará para inicializar o novo NIC para o sistema.
9.  Tente rdp na sua máquina. Se for bem sucedido, pode alterar o endereço IP privado de volta para o original, se quiser. Caso contrário, pode ficar com isto. 

#### <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

1. Certifique-se de que tem [o mais recente Azure PowerShell](/powershell/azure/) instalado
2. Abra uma sessão Azure PowerShell elevada (Executar como administrador). Execute os seguintes comandos:

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
3. Tente rdp na sua máquina.  Se for bem sucedido, pode alterar o endereço IP privado de volta para o original, se quiser. Caso contrário, pode ficar com isto.

### <a name="for-classic-vms"></a>Para VMs clássicos

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Para redefinir a interface de rede, siga estes passos:

#### <a name="use-azure-portal"></a>Utilizar o portal do Azure

1.  Aceda ao [portal do Azure]( https://ms.portal.azure.com).
2.  Selecione **Máquinas Virtuais (Clássicas)**.
3.  Selecione a Máquina Virtual afetada.
4.  Selecione **endereços IP**.
5.  Se a **atribuição de IP privada**  não for  **estática,** altere-a para **Estática**.
6.  Altere o **endereço IP** para outro endereço IP disponível na Sub-rede.
7.  Selecione **Guardar**.
8.  A máquina virtual reiniciará para inicializar o novo NIC para o sistema.
9.  Tente rdp na sua máquina. Se for bem sucedido, pode optar por reverter o endereço IP privado de volta para o original.  

#### <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

1. Certifique-se de que tem [o mais recente Azure PowerShell](/powershell/azure/) instalado.
2. Abra uma sessão Azure PowerShell elevada (Executar como administrador). Execute os seguintes comandos:

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
3. Tente rdp na sua máquina. Se for bem sucedido, pode alterar o endereço IP privado de volta para o original, se quiser. Caso contrário, pode ficar com isto. 

## <a name="delete-the-unavailable-nics"></a>Eliminar os NICs indisponíveis
Depois de poder fazer um ambiente de trabalho remoto para a máquina, tem de eliminar os antigos NICs para evitar o problema potencial:

1.  Abra o Gestor de Dispositivos.
2.  Selecione **View**  >  **Ver Mostrar dispositivos ocultos**.
3.  Selecione **adaptadores de rede**. 
4.  Verifique os adaptadores denominados "Adaptador de Rede Hiper-V da Microsoft".
5.  Pode ver um adaptador indisponível que está acinzentado. Clique com o botão direito no adaptador e, em seguida, selecione Desinstalar.

    ![a imagem do NIC](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Apenas desinstalar os adaptadores indisponíveis que têm o nome "Adaptador de Rede Hiper-V da Microsoft". Se desinstalar algum dos outros adaptadores ocultos, poderá causar problemas adicionais.
    >
    >

6.  Agora todos os adaptadores indisponíveis devem ser limpos do seu sistema.
