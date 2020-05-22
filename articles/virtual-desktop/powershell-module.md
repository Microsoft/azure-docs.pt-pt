---
title: Módulo PowerShell Windows Ambiente de trabalho virtual - Azure
description: Como instalar e configurar o módulo PowerShell para o Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fd854691203361847ae9a6c873121c9b66820a90
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743379"
---
# <a name="set-up-the-powershell-module-for-windows-virtual-desktop"></a>Configurar o módulo PowerShell para o Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização da primavera de 2020 com os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows.
>
> A atualização Do Windows Virtual Desktop Spring 2020 encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-la para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

O módulo PowerShell do Windows Virtual Desktop PowerShell está integrado no módulo PowerShell Azure. Este artigo dir-lhe-á como configurar o módulo PowerShell para que possa executar cmdlets para o Windows Virtual Desktop.

## <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell

Para começar com a utilização do módulo, instale primeiro a [versão mais recente do PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core). Atualmente, os cmdlets do Windows Virtual Desktop funcionam apenas com o PowerShell Core.

Em seguida, terá de instalar o módulo DesktopVirtualization para utilizar na sua sessão PowerShell.

Executar o seguinte cmdlet PowerShell em modo elevado para instalar o módulo:

```powershell
Install-Module -Name Az.DesktopVirtualization
```

>[!NOTE]
> Se este cmdlet não funcionar, tente executá-lo novamente com permissões elevadas.

Em seguida, corra o seguinte cmdlet para ligar ao Azure:

```powershell
Connect-AzAccount
```

Iniciar sessão na sua conta Azure requer um código gerado quando executa o cmdlet Connect. Para iniciar sessão, vá para <https://microsoft.com/devicelogin> , insira o código e, em seguida, inscreva-se usando as suas credenciais de administrador Azure.

```powershell
Account SubscriptionName TenantId Environment

------- ---------------- -------- -----------

Youradminupn subscriptionname AzureADTenantID AzureCloud
```

Isto irá inscrevê-lo diretamente na subscrição que é padrão para as suas credenciais de administração.

## <a name="change-the-default-subscription"></a>Alterar a subscrição por defeito

Se quiser alterar a subscrição predefinida depois de ter assinado o contrato, execute este cmdlet:

```powershell
Select-AzSubscription -Subscription <preferredsubscriptionname>
```

Também pode selecionar a partir de uma lista utilizando o cmdlet Out-GridView:
```powershell
Get-AzSubscription | Out-GridView -PassThru | Select-AzSubscription
```

Quando seleciona uma nova subscrição para utilizar, não precisa de especificar o ID da subscrição em cmdlets que executa depois. Por exemplo, o seguinte cmdlet recupera um anfitrião de sessão específico sem precisar do ID de subscrição:

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname>
```

Também pode alterar as subscrições por cmdlet adicionando o nome de subscrição desejado como parâmetro. O próximo cmdlet é o mesmo que o exemplo anterior, exceto com o ID de subscrição adicionado como parâmetro para alterar a subscrição que o cmdlet utiliza.

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -SubscriptionId <subscriptionGUID>
```

## <a name="get-locations"></a>Obter localizações

O parâmetro de localização é obrigatório para todos os cmdlets **New-AzWVD** que criam novos objetos.

Execute o seguinte cmdlet para obter uma lista de locais que os seus suportes de subscrição:

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

Assim que souber esquecê-la, pode usá-la num cmdlet. Por exemplo, aqui está um cmdlet que cria uma piscina de acolhimento no local "southeastasia":

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -Location “southeastasia”
```

## <a name="next-steps"></a>Passos seguintes

Agora que configurao o seu módulo PowerShell, pode executar cmdlets para fazer todo o tipo de coisas no Windows Virtual Desktop. Aqui estão alguns dos locais onde pode usar o seu módulo:

- Passe pelos [nossos tutoriais do Windows Virtual Desktop]() para configurar o seu próprio ambiente de trabalho virtual windows virtual.
- [Criar um conjunto de anfitriões com o PowerShell](create-host-pools-powershell.md)
- [Configurar o método de balanceamento de carga do Windows Virtual Desktop](configure-host-pool-load-balancing.md)
- [Configure o tipo pessoal de atribuição de piscina de anfitrião do ambiente de trabalho](configure-host-pool-personal-desktop-assignment-type.md)
- E muito mais!

Se tiver algum problema, consulte o nosso artigo de resolução de [problemas da PowerShell](troubleshoot-powershell.md) para obter ajuda.

