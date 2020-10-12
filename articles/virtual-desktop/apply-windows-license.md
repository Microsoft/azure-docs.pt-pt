---
title: Aplicar licença do Windows para session host máquinas virtuais - Azure
description: Descreve como aplicar a licença do Windows para VMs de ambiente de trabalho virtuais do Windows.
author: ChristianMontoya
ms.topic: how-to
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: 5f3749be36f5f035e49fcb862f92180e4902101f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88010145"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Aplicar licença do Windows para session host máquinas virtuais

Os clientes que estão devidamente licenciados para executar as cargas de trabalho do Windows Virtual Desktop são elegíveis para aplicar uma licença Windows às suas máquinas virtuais de anfitrião de sessão e executá-las sem pagar outra licença. Para obter mais informações, consulte [os preços do Windows Virtual Desktop.](https://azure.microsoft.com/pricing/details/virtual-desktop/)

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>Formas de utilizar a sua licença de ambiente de trabalho virtual do Windows
O licenciamento virtual do Windows Desktop permite-lhe aplicar uma licença a qualquer máquina virtual do Windows ou Do Windows Server que esteja registada como anfitrião de sessão numa piscina de anfitriões e receba ligações do utilizador. Esta licença não se aplica a máquinas virtuais que estejam a funcionar como servidores de partilha de ficheiros, controladores de domínio, e assim por diante.

Existem algumas formas de utilizar a licença de ambiente de trabalho virtual do Windows:
- Pode criar uma piscina de anfitriões e as suas máquinas virtuais de anfitrião de sessão utilizando a oferta do [Azure Marketplace.](./create-host-pools-azure-marketplace.md) As máquinas virtuais criadas desta forma têm automaticamente a licença aplicada.
- Pode criar uma piscina de anfitriões e as suas máquinas virtuais de anfitrião de sessão utilizando o [modelo GitHub Azure Resource Manager.](./virtual-desktop-fall-2019/create-host-pools-arm-template.md) As máquinas virtuais criadas desta forma têm automaticamente a licença aplicada.
- Pode aplicar uma licença a uma máquina virtual de anfitrião de sessão existente. Para isso, siga primeiro as instruções em [Criar uma piscina de anfitrião com PowerShell](./create-host-pools-powershell.md) para criar uma piscina de anfitrião e VMs associados, em seguida, voltar a este artigo para aprender a aplicar a licença.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Aplicar uma licença do Windows a um VM anfitrião de sessão
Certifique-se de que [instalou e configura o mais recente Azure PowerShell](/powershell/azure/). Executar o seguinte cmdlet PowerShell para aplicar a licença Windows:

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Verifique se o VM do anfitrião da sessão está a utilizar o benefício do licenciamento
Depois de implementar o seu VM, execute este cmdlet ot verifique o tipo de licença:
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

Um VM anfitrião de sessão com a licença de Windows aplicada irá mostrar-lhe algo assim:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

VMs sem a licença do Windows aplicada irá mostrar-lhe algo assim:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Execute o seguinte cmdlet para ver uma lista de todos os VMs do anfitrião da sessão que tenham a licença Windows aplicada na sua subscrição Azure:

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```
