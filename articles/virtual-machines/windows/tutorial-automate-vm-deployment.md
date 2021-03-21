---
title: Tutorial - Instalar aplicações num Windows VM em Azure
description: Neste tutorial, vai aprender a utilizar a Extensão de Script Personalizado para executar scripts e implementar aplicações em máquinas virtuais do Windows no Azure
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c7c0fc8aaa3779390060ae283f1427f4955ed421
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555980"
---
# <a name="tutorial---deploy-applications-to-a-windows-virtual-machine-in-azure-with-the-custom-script-extension"></a>Tutorial – Implementar aplicações numa máquina virtual do Windows no Azure com a Extensão de Script Personalizado

Para configurar máquinas virtuais (VMs) de forma rápida e consistente, pode utilizar a [Extensão de Script Personalizada para Windows](../extensions/custom-script-windows.md). Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Utilizar a Extensão de Script Personalizado para instalar o IIS
> * Criar uma VM que utilize a Extensão de Script Personalizado
> * Ver um site IIS em execução após a extensão ser aplicada

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell num separador de navegador indo para [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

## <a name="custom-script-extension-overview"></a>Visão geral da extensão de script personalizado
A Extensão de Script Personalizado transfere e executa scripts em VMs do Azure. Esta extensão é útil para a configuração pós-implementação, instalação de software ou qualquer outra tarefa de gestão/configuração. Os scripts podem ser transferidos a partir do armazenamento do Azure ou do GitHub, ou fornecidos para o portal do Azure no runtime da extensão.

A extensão de Script Personalizado é integrada em modelos do Azure Resource Manager, podendo também ser executada ao utilizar a CLI do Azure, PowerShell, portal do Azure ou API REST de Máquinas Virtuais do Azure.

Pode utilizar a Extensão de Script Personalizado com VMs do Windows e do Linux.


## <a name="create-virtual-machine"></a>Criar a máquina virtual
Desajuste o nome de utilizador e a palavra-passe do administrador para o VM com [o Get-Credential:](/powershell/module/microsoft.powershell.security/get-credential)

```azurepowershell-interactive
$cred = Get-Credential
```

Agora pode criar o VM com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo seguinte cria uma VM com o nome *myVM* na localização *EastUS*. Se ainda não existirem, serão criados os grupos de recursos *myResourceGroupAutomate* e recursos de rede de apoio. Para permitir um tráfego Web, o cmdlet também abre a porta *80*.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Credential $cred
```

Demora alguns minutos até que os recursos e a VM sejam criados.


## <a name="automate-iis-install"></a>Automatizar a instalação do IIS
Utilize [a Configuração AzVMExtension](/powershell/module/az.compute/set-azvmextension) para instalar a extensão de script personalizada. A extensão executa o `powershell Add-WindowsFeature Web-Server` para instalar o servidor Web IIS e, em seguida, atualiza a página *Default.htm* para mostrar o nome do anfitrião da VM:

```azurepowershell-interactive
Set-AzVMExtension -ResourceGroupName "myResourceGroupAutomate" `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location "EastUS" `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```


## <a name="test-web-site"></a>Site de teste
Obtenha o endereço IP público do seu equilibrador de carga com [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). O exemplo seguinte obtém o endereço IP para *myPublicIPAddress*, criado anteriormente:

```azurepowershell-interactive
Get-AzPublicIPAddress `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myPublicIPAddress" | select IpAddress
```

Em seguida, pode introduzir o endereço IP público num browser. O site é apresentado, incluindo o nome do anfitrião da VM para a qual o balanceador de carga distribuiu tráfego, como no seguinte exemplo:

![Site do IIS em execução](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, automatizou a instalação do IIS numa VM. Aprendeu a:

> [!div class="checklist"]
> * Utilizar a Extensão de Script Personalizado para instalar o IIS
> * Criar uma VM que utilize a Extensão de Script Personalizado
> * Ver um site IIS em execução após a extensão ser aplicada

Avance para o tutorial seguinte para aprender a criar imagens de VM personalizadas.

> [!div class="nextstepaction"]
> [Criar imagens de VM personalizadas](./tutorial-custom-images.md)
