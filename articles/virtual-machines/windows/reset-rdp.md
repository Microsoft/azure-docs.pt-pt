---
title: "Repor a palavra-passe ou a configuração do ambiente de trabalho remoto numa VM do Windows | Microsoft Docs"
description: "Saiba como repor uma palavra-passe de conta ou serviços de ambiente de trabalho remoto numa VM do Windows utilizando o portal do Azure ou o Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/06/2017
ms.author: genli
ms.openlocfilehash: b5c6c6e06f6e4173730e6b030b86f443c58aa0f0
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2017
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Como repor o serviço de ambiente de trabalho remoto ou a palavra-passe de início de sessão numa VM do Windows
Se não é possível ligar a uma máquina virtual (VM) do Windows, pode repor a palavra-passe de administrador local ou reponha a configuração do serviço de ambiente de trabalho remoto (não suportada em controladores de domínio do Windows). Pode utilizar o portal do Azure ou a extensão de acesso de VM no Azure PowerShell para repor a palavra-passe. Se estiver a utilizar o PowerShell, certifique-se de que tem o [módulo do PowerShell mais recente instalado e configurado](/powershell/azure/overview) e a sessão iniciada na sua subscrição do Azure. Também pode [executar estes passos para VMs criadas com o modelo de implementação clássica](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

## <a name="ways-to-reset-configuration-or-credentials"></a>Formas de repor as credenciais ou de configuração
Pode repor os serviços de ambiente de trabalho remoto e as credenciais de diversas formas, consoante as suas necessidades:

- [Reposição através do portal do Azure](#azure-portal)
- [Repor com o Azure PowerShell](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Portal do Azure
Para expandir o menu do portal, clique nas barras de três no canto superior esquerdo e, em seguida, clique em **máquinas virtuais**:

![Navegue para a VM do Azure](./media/reset-rdp/Portal-Select-VM.png)

### <a name="reset-the-local-administrator-account-password"></a>**Repor a palavra-passe da conta de administrador local**

Selecione a máquina virtual do Windows, em seguida, clique em **suporte + resolução de problemas** > **Repor palavra-passe**. É apresentado o painel de reposição de palavra-passe:

![Página de reposição de palavra-passe](./media/reset-rdp/Portal-RM-PW-Reset-Windows.png)

Introduza o nome de utilizador e uma palavra-passe nova, em seguida, clique em **atualização**. Tente ligar novamente à VM.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Repor a configuração do serviço de ambiente de trabalho remoto**

Selecione a máquina virtual do Windows, em seguida, clique em **suporte + resolução de problemas** > **Repor palavra-passe**. É apresentado o painel de reposição de palavra-passe. 

![Repor configuração RDP](./media/reset-rdp/Portal-RM-RDP-Reset.png)

Selecione **apenas a configuração de reposição** no menu pendente, em seguida, clique em **atualização**. Tente ligar novamente à VM.


## <a name="vmaccess-extension-and-powershell"></a>Extensão VMAccess e PowerShell
Certifique-se de que tem o [módulo do PowerShell mais recente instalado e configurado](/powershell/azure/overview) e a sessão iniciada na sua subscrição do Azure com o `Login-AzureRmAccount` cmdlet.

### <a name="reset-the-local-administrator-account-password"></a>**Repor a palavra-passe da conta de administrador local**
Repor o nome de utilizador ou palavra-passe de administrador com a [conjunto AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) cmdlet do PowerShell. Crie as credenciais da conta da seguinte forma:

```powershell
$cred=Get-Credential
```

> [!NOTE] 
> Se escrever um nome diferente a atual conta de administrador local no VM, a extensão VMAccess irá adicionar uma conta de administrador local com esse nome e atribuir a palavra-passe especificada para essa conta. Se a conta de administrador local no VM, irá repor a palavra-passe e se a conta está desativada, a extensão VMAccess ativa-o.


O exemplo seguinte atualiza a VM com o nome `myVM` no grupo de recursos denominado `myResourceGroup` para as credenciais especificadas.

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -UserName $cred.GetNetworkCredential().UserName -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

### <a name="reset-the-remote-desktop-service-configuration"></a>**Repor a configuração do serviço de ambiente de trabalho remoto**
Reponha o acesso remoto à VM com o [conjunto AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) cmdlet do PowerShell. O exemplo seguinte repõe a extensão de acesso com o nome `myVMAccess` na VM com o nome `myVM` no `myResourceGroup` grupo de recursos:

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
```

> [!TIP]
> Em qualquer momento, uma VM pode ter apenas um único acesso agente da VM. Para configurar o acesso VM propriedades do agente com êxito, o `-ForceRerun` opção pode ser utilizada. Quando utilizar `-ForceRerun`, certifique-se de que utiliza o mesmo nome para o agente de acesso VM, conforme utilizado em qualquer comandos anteriores.

Se ainda não é possível ligar remotamente à máquina virtual, consulte mais passos para tentar em [ligações de resolução de problemas de ambiente de trabalho remoto para uma máquina virtual do Azure baseados em Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


## <a name="next-steps"></a>Passos seguintes
Se a extensão de acesso de VM do Azure não responde e não conseguir repor a palavra-passe, pode [repor o Windows palavra-passe local offline](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Este método é um processo mais avançado e requer que ligue o disco rígido virtual da VM problemático para outra VM. Siga os passos documentados neste artigo pela primeira vez e apenas tente o método de reposição de palavra-passe offline como último recurso.

[Extensões VM do Azure e funcionalidades](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Ligar a uma máquina virtual do Azure com RDP ou SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Resolver problemas de ligações de ambiente de trabalho remoto para uma máquina virtual baseado no Windows Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

