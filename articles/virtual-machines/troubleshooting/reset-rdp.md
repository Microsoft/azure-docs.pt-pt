---
title: Redefinir Serviços de Área de Trabalho Remota ou sua senha de administrador em uma VM do Windows | Microsoft Docs
description: Saiba como redefinir uma senha de conta ou Serviços de Área de Trabalho Remota em uma VM do Windows usando o portal do Azure ou Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/25/2019
ms.author: genli
ms.openlocfilehash: 580ec443dc087f270e30856c336a5699bbf1ae71
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058450"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>Redefinir Serviços de Área de Trabalho Remota ou sua senha de administrador em uma VM do Windows
Se não for possível se conectar a uma VM (máquina virtual) do Windows, você poderá redefinir sua senha de administrador local ou redefinir a configuração de Serviços de Área de Trabalho Remota (sem suporte em controladores de domínio do Windows). Para repor a palavra-passe, utilize o portal do Azure ou a extensão VM Access, no Azure PowerShell. Depois de iniciar sessão na VM, reponha a palavra-passe desse administrador local.  
Se você estiver usando o PowerShell, verifique se você tem o [módulo mais recente do PowerShell instalado e configurado](/powershell/azure/overview) e se está conectado à sua assinatura do Azure. Também pode [seguir estes passos para VMs criadas com o modelo de implementação clássica](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

Você pode redefinir Serviços de Área de Trabalho Remota e credenciais das seguintes maneiras:

- [Redefinir usando o portal do Azure](#reset-by-using-the-azure-portal)

- [Redefinir usando a extensão VMAccess e o PowerShell](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>Redefinir usando o portal do Azure

Primeiro, entre no [portal do Azure](https://portal.azure.com) e, em seguida, selecione **máquinas virtuais** no menu à esquerda. 

### <a name="reset-the-local-administrator-account-password"></a>**Redefinir a senha da conta de administrador local**

1. Selecione sua VM do Windows e, em seguida, selecione **Redefinir senha** em **suporte + solução de problemas**. A janela **Redefinir senha** é exibida.

2. Selecione **Redefinir senha**, insira um nome de usuário e uma senha e, em seguida, selecione **Atualizar**. 

3. Tente se conectar à VM novamente.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Redefinir a configuração de Serviços de Área de Trabalho Remota**

Esse processo habilitará Área de Trabalho Remota serviço na VM e criará uma regra de firewall para a porta RDP padrão 3389.

1. Selecione sua VM do Windows e, em seguida, selecione **Redefinir senha** em **suporte + solução de problemas**. A janela **Redefinir senha** é exibida. 

2. Selecione **Redefinir configuração somente** e, em seguida, selecione **Atualizar**. 

3. Tente se conectar à VM novamente.

## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>Redefinir usando a extensão VMAccess e o PowerShell

Primeiro, verifique se você tem o [módulo do PowerShell mais recente instalado e configurado](/powershell/azure/overview) e se está conectado à sua assinatura do Azure usando o cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

### <a name="reset-the-local-administrator-account-password"></a>**Redefinir a senha da conta de administrador local**

- Redefina a senha de administrador ou o nome de usuário com o cmdlet [set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) do PowerShell. A `typeHandlerVersion` configuração deve ser 2,0 ou maior, pois a versão 1 foi preterida. 

    ```powershell
    $SubID = "<SUBSCRIPTION ID>" 
    $RgName = "<RESOURCE GROUP NAME>" 
    $VmName = "<VM NAME>" 
    $Location = "<LOCATION>" 
 
    Connect-AzAccount 
    Select-AzSubscription -SubscriptionId $SubID 
    Set-AzVMAccessExtension -ResourceGroupName $RgName -Location $Location -VMName $VmName -Credential (get-credential) -typeHandlerVersion "2.0" -Name VMAccessAgent 
    ```

    > [!NOTE] 
    > Se você inserir um nome diferente da conta de administrador local atual em sua VM, a extensão VMAccess adicionará uma conta de administrador local com esse nome e atribuirá a senha especificada a essa conta. Se a conta de administrador local em sua VM existir, a extensão VMAccess redefinirá a senha. Se a conta estiver desabilitada, a extensão VMAccess a habilitará.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Redefinir a configuração de Serviços de Área de Trabalho Remota**

1. Redefina o acesso remoto para sua VM com o cmdlet [set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) do PowerShell. O exemplo a seguir redefine a extensão de `myVMAccess` acesso chamada na VM `myVM` chamada no `myResourceGroup` grupo de recursos:

    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
    ```

    > [!TIP]
    > A qualquer momento, uma VM pode ter apenas um agente de acesso de VM único. Para definir as propriedades do agente de acesso à VM `-ForceRerun` , use a opção. Ao usar `-ForceRerun`o, certifique-se de usar o mesmo nome para o agente de acesso à VM que você pode ter usado em qualquer comando anterior.

1. Se você ainda não conseguir se conectar remotamente à sua máquina virtual, consulte [solucionar problemas de conexões área de trabalho remota a uma máquina virtual do Azure baseada no Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Se você perder a conexão com o controlador de domínio do Windows, será necessário restaurá-lo de um backup do controlador de domínio.

## <a name="next-steps"></a>Passos Seguintes

- Se a extensão de acesso à VM do Azure não responder e você não conseguir redefinir a senha, você poderá [redefinir a senha do Windows local offline](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Esse método é mais avançado e exige que você conecte o disco rígido virtual da VM problemática a outra VM. Siga as etapas documentadas neste artigo primeiro e tente o método de redefinição de senha offline somente se essas etapas não funcionarem.

- [Saiba mais sobre os recursos e extensões de VM do Azure](../extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

- [Conecte-se a uma máquina virtual do Azure com RDP ou SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

- [Solucionar problemas de conexões área de trabalho remota a uma máquina virtual do Azure baseada no Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

