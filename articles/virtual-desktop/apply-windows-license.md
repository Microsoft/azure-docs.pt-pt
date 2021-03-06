---
title: Aplicar licença do Windows para session host máquinas virtuais - Azure
description: Descreve como aplicar a licença do Windows para VMs de ambiente de trabalho virtuais do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 08/14/2019
ms.author: helohr
ms.openlocfilehash: fa3c9f82e99536b07a27656e0143d6b2fcc89a44
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833873"
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

## <a name="requirements-for-deploying-windows-server-remote-desktop-services"></a>Requisitos para implementar serviços de ambiente de trabalho remoto do Windows Server

Se implementar o Windows Server 2019, 2016 ou 2012 R2 como anfitriões do Windows Virtual Desktop na sua implementação, um servidor de licença de Serviços de Ambiente de Trabalho Remoto deve estar acessível a partir dessas máquinas virtuais. O servidor de licença remote desktop Services pode ser localizado no local ou em Azure. Para obter mais informações, consulte [Ativar o servidor de licença remote desktop Services](/windows-server/remote/remote-desktop-services/rds-activate-license-server).
