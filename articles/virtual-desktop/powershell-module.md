---
title: PowerShell module Windows Virtual Desktop - Azure
description: Como instalar e configurar o módulo PowerShell para o Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: fdd74a09e49a352d18cf4ceccf406a25bf6dffb3
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445844"
---
# <a name="set-up-the-powershell-module-for-windows-virtual-desktop"></a>Configurar o módulo PowerShell para o Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com integração do Azure Resource Manager.

O módulo Virtual Desktop PowerShell do Windows está integrado no módulo Azure PowerShell. Este artigo dir-lhe-á como configurar o módulo PowerShell para que possa executar cmdlets para o Windows Virtual Desktop.

## <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell

Para começar a utilizar o módulo, instale primeiro a [versão mais recente do PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core). Atualmente, os cmdlets virtual do Windows Desktop funcionam apenas com o PowerShell Core.

Em seguida, terá de instalar o módulo desktopVirtualization para utilizar na sessão PowerShell.

Executar o seguinte cmdlet PowerShell em modo elevado para instalar o módulo:

```powershell
Install-Module -Name Az.DesktopVirtualization
```

>[!NOTE]
> Se este cmdlet não funcionar, tente executá-lo novamente com permissões elevadas.

Em seguida, executar o seguinte cmdlet para ligar a Azure:

```powershell
Connect-AzAccount
```

>[!IMPORTANT]
>Se estiver a ligar-se ao portal Us Gov, execute este cmdlet em vez disso:
> 
> ```powershell
> Connect-AzAccount -EnvironmentName AzureUSGovernment
> ```

A inscrição na sua conta Azure requer um código gerado quando executam o cmdlet Connect. Para iniciar sação, vá, <https://microsoft.com/devicelogin> introduza o código e, em seguida, inscreva-se usando as suas credenciais de administração Azure.

```powershell
Account SubscriptionName TenantId Environment

------- ---------------- -------- -----------

Youradminupn subscriptionname AzureADTenantID AzureCloud
```

Isto irá inscrevê-lo diretamente na subscrição que é padrão para as suas credenciais de administração.

## <a name="change-the-default-subscription"></a>Alterar a subscrição padrão

Se quiser alterar a subscrição predefinida depois de ter assinado, execute este cmdlet:

```powershell
Select-AzSubscription -Subscription <preferredsubscriptionname>
```

Também pode selecionar uma de uma lista utilizando o Out-GridView cmdlet:

```powershell
Get-AzSubscription | Out-GridView -PassThru | Select-AzSubscription
```

Quando selecionar uma nova subscrição para usar, não precisa de especificar o ID da subscrição em cmdlets que executar depois. Por exemplo, o seguinte cmdlet recupera um anfitrião de sessão específico sem precisar do ID de subscrição:

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname>
```

Também pode alterar subscrições por cmdlet adicionando o nome de subscrição pretendido como parâmetro. O cmdlet seguinte é o mesmo que o exemplo anterior, exceto com o ID de subscrição adicionado como um parâmetro para alterar a subscrição que o cmdlet usa.

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -SubscriptionId <subscriptionGUID>
```

## <a name="get-locations"></a>Obter localizações

O parâmetro de localização é obrigatório para todos os cmdlets **New-AzWVD** que criam novos objetos.

Execute o seguinte cmdlet para obter uma lista de locais que a sua subscrição suporta:

```powershell
Get-AzLocation
```

A saída para **Get-AzLocation** será assim:

```powershell
Location : eastasia

DisplayName : East Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : southeastasia

DisplayName : Southeast Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : centralus

DisplayName : Central US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}

Location : eastus

DisplayName : East US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}
```

Assim que souber a localização da sua conta, pode usá-la num cmdlet. Por exemplo, aqui está um cmdlet que cria uma piscina de anfitriões na localização "southeastasia":

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -Location “southeastasia”
```

## <a name="next-steps"></a>Passos seguintes

Agora que configuraste o teu módulo PowerShell, podes executar cmdlets para fazer todo o tipo de coisas no Windows Virtual Desktop. Aqui estão alguns dos locais onde pode utilizar o seu módulo:

- Corra através [dos nossos tutoriais de Desktop Virtual do Windows]() para configurar o seu próprio ambiente de trabalho virtual windows.
- [Criar um conjunto de anfitriões com o PowerShell](create-host-pools-powershell.md)
- [Configurar o método de balanceamento de carga do Windows Virtual Desktop](configure-host-pool-load-balancing.md)
- [Configure o tipo de atribuição de piscina de anfitriões de ambiente de trabalho pessoal](configure-host-pool-personal-desktop-assignment-type.md)
- E muito mais!

Se encontrar algum problema, consulte o nosso [artigo de resolução de problemas da PowerShell](troubleshoot-powershell.md) para obter ajuda.

