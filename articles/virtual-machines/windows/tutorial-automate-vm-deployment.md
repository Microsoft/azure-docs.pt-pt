---
title: Tutorial – Instalar aplicações numa VM do Windows no Azure | Microsoft Docs
description: Neste tutorial, vai aprender a utilizar a Extensão de Script Personalizado para executar scripts e implementar aplicações em máquinas virtuais do Windows no Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 72489c6d0b03166fcb2a5f1ed39f7b8d5408ff24
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708184"
---
# <a name="tutorial---deploy-applications-to-a-windows-virtual-machine-in-azure-with-the-custom-script-extension"></a>Tutorial – Implementar aplicações numa máquina virtual do Windows no Azure com a Extensão de Script Personalizado

Para configurar máquinas virtuais (VMs) de forma rápida e consistente, pode utilizar o [extensão de Script personalizado para o Windows](extensions-customscript.md). Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Utilizar a Extensão de Script Personalizado para instalar o IIS
> * Criar uma VM que utilize a Extensão de Script Personalizado
> * Ver um site IIS em execução após a extensão ser aplicada

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

## <a name="custom-script-extension-overview"></a>Visão geral da extensão de script personalizado
A Extensão de Script Personalizado transfere e executa scripts em VMs do Azure. Esta extensão é útil para a configuração pós-implementação, instalação de software ou qualquer outra tarefa de gestão/configuração. Os scripts podem ser transferidos a partir do armazenamento do Azure ou do GitHub, ou fornecidos para o portal do Azure no runtime da extensão.

A extensão de Script Personalizado é integrada em modelos do Azure Resource Manager, podendo também ser executada ao utilizar a CLI do Azure, PowerShell, portal do Azure ou API REST de Máquinas Virtuais do Azure.

Pode utilizar a Extensão de Script Personalizado com VMs do Windows e do Linux.


## <a name="create-virtual-machine"></a>Criar a máquina virtual
Definir o nome de utilizador de administrador e a palavra-passe para a VM com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora, pode criar a VM com [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). O exemplo seguinte cria uma VM com o nome *myVM* na localização *EastUS*. Se ainda não existirem, serão criados os grupos de recursos *myResourceGroupAutomate* e recursos de rede de apoio. Para permitir um tráfego Web, o cmdlet também abre a porta *80*.

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
Uso [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) para instalar a extensão de Script personalizado. A extensão executa o `powershell Add-WindowsFeature Web-Server` para instalar o servidor Web IIS e, em seguida, atualiza a página *Default.htm* para mostrar o nome do anfitrião da VM:

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
Obtenha o endereço IP público do seu Balanceador de carga com [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). O exemplo seguinte obtém o endereço IP para *myPublicIPAddress*, criado anteriormente:

```azurepowershell-interactive
Get-AzPublicIPAddress `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myPublicIPAddress" | select IpAddress
```

Em seguida, pode introduzir o endereço IP público num browser. O site é apresentado, incluindo o nome do anfitrião da VM para a qual o balanceador de carga distribuiu tráfego, como no seguinte exemplo:

![Site do IIS em execução](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, automatizou a instalação do IIS numa VM. Aprendeu a:

> [!div class="checklist"]
> * Utilizar a Extensão de Script Personalizado para instalar o IIS
> * Criar uma VM que utilize a Extensão de Script Personalizado
> * Ver um site IIS em execução após a extensão ser aplicada

Avance para o tutorial seguinte para aprender a criar imagens de VM personalizadas.

> [!div class="nextstepaction"]
> [Criar imagens de VM personalizadas](./tutorial-custom-images.md)
