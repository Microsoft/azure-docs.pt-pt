---
title: Quickstart - Criar um Windows VM com Azure PowerShell
description: Neste guia de início rápido, irá aprender a utilizar o Azure PowerShell para criar uma máquina virtual do Windows
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 07/02/2019
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 78b912dd649ff942e0187f9b3602d9213383b8c9
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560740"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-azure-with-powershell"></a>Início Rápido: Criar uma máquina virtual do Windows no Azure com o PowerShell

O módulo do Azure PowerShell é utilizado para criar e gerir recursos do Azure a partir da linha de comandos do PowerShell ou em scripts. Este guia de início rápido mostra como utilizar o módulo do Azure PowerShell para implementar uma máquina virtual (VM) no Azure que executa o Windows Server 2016. Também irá rdp para o VM e instalará o servidor web IIS, para mostrar o VM em ação.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell num separador de navegador indo para [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.


## <a name="create-resource-group"></a>Criar grupo de recursos

Criar um grupo de recursos Azure com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-virtual-machine"></a>Criar a máquina virtual

Criar um VM com [New-AzVM](/powershell/module/az.compute/new-azvm). Forneça nomes para cada um dos recursos e o `New-AzVM` cmdlet cria se eles já não existirem.

Quando solicitado, forneça um nome de utilizador e uma palavra-passe para serem usados como credenciais de entrada para o VM:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389
```

## <a name="connect-to-virtual-machine"></a>Conectar à máquina virtual

Após a conclusão da implementação, estabeleça o RDP para a VM. Para ver a VM em ação, o servidor Web do IIS é instalado.

Para ver o endereço IP público do VM, utilize o cmdlet [Get-AzPublicIpAddress:](/powershell/module/az.network/get-azpublicipaddress)

```powershell
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Utilize o seguinte comando para criar uma sessão de ambiente de trabalho remoto a partir do computador local. Substitua o endereço IP pelo endereço IP público da VM. 

```powershell
mstsc /v:publicIpAddress
```

Na janela **Segurança do Windows**, selecione **Mais opções** e, em seguida, selecione **Utilizar uma conta diferente**. Digite o nome de utilizador como nome **de** \\ *utilizador* local, introduza a palavra-passe que criou para a máquina virtual e, em seguida, clique em **OK**.

Poderá receber um aviso de certificado durante o processo de início de sessão. Clique **em Sim** ou Continue **a** criar a ligação

## <a name="install-web-server"></a>Instalar o servidor Web

Para ver a VM em ação, instale o servidor Web do IIS. Abra uma janela do PowerShell na VM e execute o comando seguinte:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Quando terminar, feche a ligação RDP à VM.

## <a name="view-the-web-server-in-action"></a>Ver o servidor Web em ação

Com o IIS instalado e a porta 80 aberta na VM a partir da Internet, utilize um browser à sua escolha para ver a página de boas-vindas do IIS predefinida. Utilize o endereço IP público da VM que obteve no passo anterior. O seguinte exemplo mostra o site do IIS predefinido:

![Site predefinido do IIS](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, pode utilizar o cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, VM e todos os recursos relacionados:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, implementou uma máquina virtual simples, abriu uma porta de rede para o tráfego Web e instalou um servidor Web básico. Para saber mais sobre as máquinas virtuais do Azure, continue com o tutorial para VMs do Windows.

> [!div class="nextstepaction"]
> [Tutoriais de máquinas virtuais do Windows do Azure](./tutorial-manage-vm.md)
