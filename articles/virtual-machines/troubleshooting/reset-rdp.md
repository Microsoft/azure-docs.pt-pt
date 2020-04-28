---
title: Redefinir Serviços de Ambiente de Trabalho Remoto ou a sua palavra-passe de administrador num VM do Windows / Microsoft Docs
description: Saiba como redefinir uma palavra-passe de conta ou serviços de ambiente de trabalho remotonum Windows VM utilizando o portal Azure ou o Azure PowerShell.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "71058450"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>Redefinir serviços de ambiente de trabalho remotos ou a sua palavra-passe do administrador num VM do Windows
Se não conseguir ligar-se a uma máquina virtual do Windows (VM), pode redefinir a sua palavra-passe do administrador local ou redefinir a configuração dos Serviços de Ambiente de Trabalho Remoto (não suportados nos controladores de domínio do Windows). Para repor a palavra-passe, utilize o portal do Azure ou a extensão VM Access, no Azure PowerShell. Depois de iniciar sessão na VM, reponha a palavra-passe desse administrador local.  
Se estiver a utilizar o PowerShell, certifique-se de que tem o [mais recente módulo PowerShell instalado e configurado](/powershell/azure/overview) e está inscrito na sua subscrição Azure. Também pode [seguir estes passos para VMs criadas com o modelo de implementação clássica](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

Pode repor os Serviços de Ambiente de Trabalho Remoto e as credenciais das seguintes formas:

- [Reset usando o portal Azure](#reset-by-using-the-azure-portal)

- [Reset utilizando a extensão VMAccess e PowerShell](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>Reset usando o portal Azure

Primeiro, inscreva-se no [portal Azure](https://portal.azure.com) e, em seguida, selecione **máquinas Virtuais** no menu esquerdo. 

### <a name="reset-the-local-administrator-account-password"></a>**Redefinir a senha da conta do administrador local**

1. Selecione o seu Windows VM e, em seguida, selecione **reset password** em **Suporte + Resolução de Problemas**. A janela de **palavra-passe Reset** é visualizada.

2. Selecione **Redefinir a palavra-passe,** introduza um nome de utilizador e uma palavra-passe e, em seguida, selecione **'Atualizar**' . 

3. Tente ligar-se ao seu VM novamente.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Redefinir a configuração dos Serviços de Ambiente de Trabalho Remoto**

Este processo ativará o serviço Remote Desktop no VM e criará uma regra de firewall para a porta DERRD Padrão 3389.

1. Selecione o seu Windows VM e, em seguida, selecione **reset password** em **Suporte + Resolução de Problemas**. A janela de **palavra-passe Reset** é visualizada. 

2. Selecione apenas a **configuração de reset** e, em seguida, selecione **'Atualizar**' . 

3. Tente ligar-se ao seu VM novamente.

## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>Reset utilizando a extensão VMAccess e PowerShell

Em primeiro lugar, certifique-se de que tem o [mais recente módulo PowerShell instalado e configurado](/powershell/azure/overview) e está inscrito na sua subscrição Azure utilizando o cmdlet [Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount)

### <a name="reset-the-local-administrator-account-password"></a>**Redefinir a senha da conta do administrador local**

- Redefinir a palavra-passe do administrador ou o nome do utilizador com o [cmdlet Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) PowerShell. A `typeHandlerVersion` definição deve ser 2.0 ou superior, porque a versão 1 é depreciada. 

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
    > Se introduzir um nome diferente da conta de administrador local atual no seu VM, a extensão VMAccess adicionará uma conta de administrador local com esse nome e atribuirá a sua senha especificada a essa conta. Se a conta de administrador local no seu VM existir, a extensão VMAccess redefinirá a palavra-passe. Se a conta estiver desativada, a extensão VMAccess irá ativar.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Redefinir a configuração dos Serviços de Ambiente de Trabalho Remoto**

1. Redefinir o acesso remoto ao seu VM com o [cmdlet Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) PowerShell. O exemplo seguinte repõe a `myVMAccess` extensão de `myVM` acesso `myResourceGroup` nomeada no VM nomeado no grupo de recursos:

    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
    ```

    > [!TIP]
    > Em qualquer momento, um VM pode ter apenas um único agente de acesso VM. Para definir as propriedades do agente `-ForceRerun` de acesso VM, use a opção. Quando utilizar, `-ForceRerun`certifique-se de que utiliza o mesmo nome para o agente de acesso VM que possa ter utilizado em quaisquer comandos anteriores.

1. Se ainda não conseguir ligar-se remotamente à sua máquina virtual, consulte as ligações de ambiente de [trabalho remoto supor problemas a uma máquina virtual Azure baseada no Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Se perder a ligação ao controlador de domínio Windows, terá de restaurá-la a partir de uma cópia de segurança do controlador de domínio.

## <a name="next-steps"></a>Passos seguintes

- Se a extensão de acesso Azure VM não responder e não conseguir redefinir a palavra-passe, pode [redefinir a senha do Windows local offline](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Este método é mais avançado e requer que ligue o disco rígido virtual do VM problemático a outro VM problemático. Siga primeiro os passos documentados neste artigo e tente o método de redefinição da palavra-passe offline apenas se esses passos não funcionarem.

- [Saiba mais sobre extensões e funcionalidades Azure VM.](../extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

- [Ligue-se a uma máquina virtual Azure com RDP ou SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

- Ligações de ambiente de trabalho remoto de [resolução de problemas a uma máquina virtual Azure baseada no Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

