---
title: Reinicie os Serviços de Ambiente de Trabalho Remoto ou a sua palavra-passe de administrador num | Windows VM Microsoft Docs
description: Saiba como redefinir uma palavra-passe de conta ou serviços de ambiente de trabalho remoto num VM do Windows utilizando o portal Azure ou o Azure PowerShell.
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
ms.openlocfilehash: 720d25079e1350315c9f403a8215f650db49ceb7
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743088"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>Repor serviços de ambiente de trabalho remoto ou a sua senha de administrador num VM do Windows
Se não conseguir ligar-se a uma máquina virtual do Windows (VM), pode redefinir a palavra-passe do administrador local ou redefinir a configuração de Serviços de Ambiente de Trabalho Remoto (não suportado nos controladores de domínio do Windows). Para repor a palavra-passe, utilize o portal do Azure ou a extensão VM Access, no Azure PowerShell. Depois de iniciar sessão na VM, reponha a palavra-passe desse administrador local.  
Se estiver a utilizar o PowerShell, certifique-se de que tem o [mais recente módulo PowerShell instalado e configurado](/powershell/azure/) e está inscrito na sua subscrição Azure. Também pode [seguir estes passos para VMs criadas com o modelo de implementação clássica](/previous-versions/azure/virtual-machines/windows/classic/reset-rdp).

Pode repor os Serviços de Ambiente de Trabalho Remoto e as credenciais das seguintes formas:

- [Reinicializar utilizando o portal Azure](#reset-by-using-the-azure-portal)

- [Reinicializar utilizando a extensão VMAccess e PowerShell](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>Reinicializar utilizando o portal Azure

Primeiro, inscreva-se no [portal Azure](https://portal.azure.com) e, em seguida, selecione **máquinas Virtuais** no menu esquerdo. 

### <a name="reset-the-local-administrator-account-password"></a>**Redefinir a senha de conta do administrador local**

1. Selecione o seu VM do Windows e, em seguida, **selecione Redefinir a palavra-passe** no **Suporte + Resolução de Problemas**. É apresentada a janela **palavra-passe Reset.**

2. Selecione **Redefinir a palavra-passe,** introduza um nome de utilizador e uma palavra-passe e, em seguida, selecione **Update**. 

3. Tente ligar-se novamente ao seu VM.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Redefinir a configuração dos Serviços de Ambiente de Trabalho Remoto**

Este processo permitirá o serviço de ambiente de trabalho remoto no VM e criará uma regra de firewall para a porta RDP padrão 3389.

1. Selecione o seu VM do Windows e, em seguida, **selecione Redefinir a palavra-passe** no **Suporte + Resolução de Problemas**. É apresentada a janela **palavra-passe Reset.** 

2. Selecione **apenas a configuração do Reset** e, em seguida, selecione **Update**. 

3. Tente ligar-se novamente ao seu VM.

## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>Reinicializar utilizando a extensão VMAccess e PowerShell

Em primeiro lugar, certifique-se de que tem o [mais recente módulo PowerShell instalado e configurado](/powershell/azure/) e está inscrito na sua subscrição Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

### <a name="reset-the-local-administrator-account-password"></a>**Redefinir a senha de conta do administrador local**

- Redefinir a palavra-passe do administrador ou o nome de utilizador com o cmdlet PowerShell do [Set-AzVMAccessExtension.](/powershell/module/az.compute/set-azvmaccessextension) A `typeHandlerVersion` regulação deve ser 2.0 ou superior, porque a versão 1 está precotado. 

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
    > Se introduzir um nome diferente da conta de administrador local atual no seu VM, a extensão VMAccess adicionará uma conta de administrador local com esse nome e atribuirá a sua palavra-passe especificada a essa conta. Se a conta de administrador local na sua VM existir, a extensão VMAccess irá redefinir a palavra-passe. Se a conta estiver desativada, a extensão VMAccess irá ativá-la.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Redefinir a configuração dos Serviços de Ambiente de Trabalho Remoto**

1. Reinicie o acesso remoto ao seu VM com o cmdlet PowerShell de [Configuração Set-AzVMAccessExtension.](/powershell/module/az.compute/set-azvmaccessextension) O exemplo a seguir repõe a extensão de acesso nomeada `myVMAccess` no VM nomeado `myVM` no grupo `myResourceGroup` de recursos:

    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
    ```

    > [!TIP]
    > Em qualquer ponto, um VM pode ter apenas um único agente de acesso VM. Para definir as propriedades do agente de acesso VM, utilize a `-ForceRerun` opção. Quando `-ForceRerun` utilizar, certifique-se de que utiliza o mesmo nome para o agente de acesso VM que possa ter utilizado em comandos anteriores.

1. Se ainda não conseguir ligar-se remotamente à sua máquina virtual, consulte [as ligações de Ambiente de Trabalho Remoto de Resolução de Problemas a uma máquina virtual Azure baseada no Windows](troubleshoot-rdp-connection.md). Se perder a ligação ao controlador de domínio do Windows, terá de a restaurar a partir de uma cópia de segurança do controlador de domínio.

## <a name="next-steps"></a>Próximos passos


- Se a extensão de acesso Azure VM não for instalada, pode [resolver problemas de extensão VM](../extensions/troubleshoot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

- Se não conseguir redefinir a palavra-passe utilizando a extensão de acesso VM, pode [redefinir a palavra-passe local do Windows offline](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Este método é mais avançado e requer que ligue o disco rígido virtual do VM problemático a outro VM. Siga primeiro os passos documentados neste artigo e tente o método de reset da palavra-passe offline apenas se esses passos não funcionarem.

- [Saiba mais sobre extensões e funcionalidades Azure VM](../extensions/features-windows.md).

- [Ligue-se a uma máquina virtual Azure com RDP ou SSH](/previous-versions/azure/dn535788(v=azure.100)).


- [Resolução de problemas Ligações de ambiente de trabalho remoto a uma máquina virtual Azure baseada no Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
