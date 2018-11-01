---
title: Repor os serviços de ambiente de trabalho remoto ou a respetiva palavra-passe de administrador numa VM do Windows | Documentos da Microsoft
description: Saiba como repor uma palavra-passe de conta ou dos serviços de ambiente de trabalho remoto numa VM do Windows com o portal do Azure ou o Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 6f825d3419a8aebeae4500a383740058e984a2f2
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50420834"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>Repor os serviços de ambiente de trabalho remoto ou a respetiva palavra-passe de administrador numa VM do Windows
Se não conseguir ligar a uma máquina virtual de Windows (VM), pode repor a palavra-passe de administrador local ou repor a configuração de serviços de ambiente de trabalho remoto (não suportada em controladores de domínio do Windows). Para repor a palavra-passe, pode utilizar o portal do Azure ou a extensão de acesso à VM do Azure PowerShell. Depois de iniciar sessão na VM, repor a palavra-passe para esse administrador local.  
Se estiver a utilizar o PowerShell, certifique-se de que tem o [módulo do PowerShell mais recente instalado e configurado](/powershell/azure/overview) e tem sessão iniciada sua subscrição do Azure. Também pode [realizar estes passos para as VMs criadas com o modelo de implementação clássica](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

Pode redefinir dos serviços de ambiente de trabalho remoto e as credenciais das seguintes formas:

- [Repor através do portal do Azure](#reset-by-using-the-azure-portal)
- [Repor ao utilizar a extensão VMAccess e o PowerShell](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>Repor através do portal do Azure
Inicie sessão no portal do Azure e, em seguida, selecione **máquinas virtuais** no menu da esquerda.

### <a name="reset-the-local-administrator-account-password"></a>**Repor a palavra-passe da conta de administrador local**

Selecione a sua VM do Windows e, em seguida, selecione **Repor palavra-passe** sob **suporte + resolução de problemas**. O **Repor palavra-passe** é apresentada a janela.

Selecione **Repor palavra-passe**, introduza um nome de utilizador e uma palavra-passe e, em seguida, selecione **atualização**. Tente ligar novamente para a VM.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Repor a configuração de serviços de ambiente de trabalho remoto**

Selecione a sua VM do Windows e, em seguida, selecione **Repor palavra-passe** sob **suporte + resolução de problemas**. O **Repor palavra-passe** é apresentada a janela. 

Selecione **repor configuração apenas** e, em seguida, selecione **atualização**. Tente ligar novamente para a VM.


## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>Repor ao utilizar a extensão VMAccess e o PowerShell
Certifique-se de que tem o [módulo do PowerShell mais recente instalado e configurado](/powershell/azure/overview) e tem sessão iniciada sua subscrição do Azure utilizando o [Connect-AzureRmAccount](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) cmdlet.

### <a name="reset-the-local-administrator-account-password"></a>**Repor a palavra-passe da conta de administrador local**
Repor o nome de utilizador ou palavra-passe de administrador com o [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) cmdlet do PowerShell. O `typeHandlerVersion` definição tem de ser 2.0 ou superior, porque a versão 1 foi preterida. 

```powershell
$SubID = "<SUBSCRIPTION ID>" 
$RgName = "<RESOURCE GROUP NAME>" 
$VmName = "<VM NAME>" 
$Location = "<LOCATION>" 
 
Connect-AzureRmAccount 
Select-AzureRMSubscription -SubscriptionId $SubID 
Set-AzureRmVMAccessExtension -ResourceGroupName $RgName -Location $Location -VMName $VmName -Credential (get-credential) -typeHandlerVersion "2.0" -Name VMAccessAgent 
```

> [!NOTE] 
> Se introduzir um nome diferente do que a conta de administrador local atual na sua VM, a extensão VMAccess irá adicionar uma conta de administrador local com esse nome e atribuir a sua palavra-passe especificada para essa conta. Se a conta de administrador local na sua VM existir, a extensão VMAccess irá repor a palavra-passe. Se a conta estiver desativada, a extensão VMAccess irá ativá-lo.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Repor a configuração de serviços de ambiente de trabalho remoto**
Reponha o acesso remoto à sua VM com o [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) cmdlet do PowerShell. O exemplo seguinte repõe a extensão de acesso com o nome `myVMAccess` na VM com o nome `myVM` no `myResourceGroup` grupo de recursos:

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
```

> [!TIP]
> Em qualquer momento, uma VM pode ter apenas um agente de acesso VM único. Para definir o acesso à VM propriedades do agente, utilize o `-ForceRerun` opção. Quando utiliza `-ForceRerun`, certifique-se de que usar o mesmo nome para o agente de acesso da VM já poderá ter utilizado em todos os comandos anteriores.

Se ainda não é possível ligar remotamente à máquina virtual, veja [ligações de resolução de problemas de ambiente de trabalho remoto a uma máquina virtual do Azure com base em Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Se perder a ligação para o controlador de domínio do Windows, terá de restaurar a partir de uma cópia de segurança do controlador de domínio.

## <a name="next-steps"></a>Passos Seguintes
Se a extensão de acesso de VM do Azure não responder e não for possível repor a palavra-passe, pode [redefinir a senha Windows local offline](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Esse método é mais avançado e requer que ligue o disco rígido virtual da VM problemática para outra VM. Siga os passos documentados neste artigo, primeiro e tente o método de reposição de palavra-passe offline apenas se esses passos não funcionam.

[Saiba mais sobre as funcionalidades e extensões de VM do Azure](../extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Ligar a uma máquina virtual do Azure com RDP ou SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Resolver problemas de ligações de ambiente de trabalho remoto a uma máquina virtual baseada no Windows Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

