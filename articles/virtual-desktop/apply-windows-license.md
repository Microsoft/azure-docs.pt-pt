---
title: Aplicar licença do Windows para sessão de anfitriões de máquinas virtuais - Azure
description: Descreve como aplicar a licença Do Windows para VMs de ambiente de trabalho virtual windows.
services: virtual-desktop
author: ChristianMontoya
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: 2543dd12e8a75a038a1fc04371b8c562ef696e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254238"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Aplicar licença do Windows para sessão de anfitriões de máquinas virtuais

Os clientes devidamente licenciados para executar as cargas de trabalho do Windows Virtual Desktop são elegíveis para aplicar uma licença do Windows às suas máquinas virtuais de anfitrião de sessão e executá-las sem pagar por outra licença. Para mais informações, consulte os preços do [Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>Formas de utilizar a sua licença de ambiente de trabalho virtual do Windows
O licenciamento do Windows Virtual Desktop permite-lhe aplicar uma licença a qualquer máquina virtual do Windows ou do Windows Server que esteja registada como anfitrião de uma sessão num pool de anfitriões e que receba as ligações do utilizador. Esta licença não se aplica a máquinas virtuais que estão a funcionar como servidores de partilha de ficheiros, controladores de domínio, e assim por diante.

Existem algumas formas de utilizar a licença de ambiente de trabalho virtual do Windows:
- Você pode criar uma piscina anfitriã e sua sessão hospedar máquinas virtuais usando a [oferta Azure Marketplace](./create-host-pools-azure-marketplace.md). As máquinas virtuais criadas desta forma têm automaticamente a licença aplicada.
- Você pode criar um pool anfitrião e sua sessão anfitrião máquinas virtuais usando o [modelo GitHub Azure Resource Manager](./create-host-pools-arm-template.md). As máquinas virtuais criadas desta forma têm automaticamente a licença aplicada.
- Pode aplicar uma licença a uma máquina virtual de anfitrião de sessão existente. Para isso, siga primeiro as instruções em [Criar um pool de anfitriões com](./create-host-pools-powershell.md) a PowerShell para criar uma piscina hospedeira e VMs associados, em seguida, volte a este artigo para aprender a aplicar a licença.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Aplicar uma licença do Windows a um VM anfitrião de sessão
Certifique-se de que [instalou e configurou o mais recente Azure PowerShell](/powershell/azure/overview). Executar o seguinte cmdlet PowerShell para aplicar a licença Windows:

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Verifique se a sua vm anfitriã o anfitrião da sessão está a utilizar o benefício de licenciamento
Depois de implantar o seu VM, execute este cmdlet ot verificar o tipo de licença:
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

Um VM anfitrião de sessão com a licença do Windows aplicada irá mostrar-lhe algo assim:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

VMs sem a licença do Windows aplicada mostrar-lhe-ão algo assim:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Execute o seguinte cmdlet para ver uma lista de todos os VMs anfitriões da sessão que tenham a licença Windows aplicada na sua subscrição Azure:

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```
