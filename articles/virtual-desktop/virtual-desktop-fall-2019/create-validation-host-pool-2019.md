---
title: Windows Virtual Desktop (clássico) hospedar atualizações do serviço de piscina - Azure
description: Aprenda a criar um pool de anfitriões de validação no Windows Virtual Desktop (clássico) para monitorizar as atualizações do serviço antes de lançar atualizações para a produção.
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 291f1e8b8870257c233dc32894ff49b26c0a3501
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91323534"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates-in-windows-virtual-desktop-classic"></a>Tutorial: Criar uma piscina de anfitriões para validar atualizações de serviço no Windows Virtual Desktop (clássico)

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows. Se estiver a tentar gerir os objetos de ambiente de trabalho virtuais do Azure Resource Manager Windows, consulte [este artigo](../create-validation-host-pool.md).

As piscinas hospedeiras são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de inquilinos do Windows Virtual Desktop. Recomendamos que crie um pool de anfitriões de validação onde as atualizações de serviço são aplicadas primeiro. Isto permite-lhe monitorizar as atualizações de serviço antes que o serviço as aplique ao seu ambiente padrão ou não de validação. Sem um pool de anfitriões de validação, pode não descobrir alterações que introduzam erros, o que pode resultar em tempo de inatividade para os utilizadores no seu ambiente de produção.

Para garantir que as suas aplicações funcionam com as últimas atualizações, o pool de anfitriões de validação deve ser o mais similar aos pools anfitriões no seu ambiente de não validação. Os utilizadores devem ligar-se com tanta frequência à piscina do anfitrião de validação como fazem com a piscina de anfitrião padrão. Se tiver testes automatizados na sua piscina de anfitriões, deverá incluir testes automatizados na piscina do anfitrião de validação.

Pode depurar problemas no pool do anfitrião de validação com [a funcionalidade de diagnóstico](diagnostics-role-service-2019.md) ou com os [artigos de resolução de problemas do Windows Virtual Desktop](troubleshoot-set-up-overview-2019.md).

>[!NOTE]
> Recomendamos que deixe a piscina de anfitriões de validação no lugar para testar todas as atualizações futuras.

Antes de começar, [descarregue e importe o módulo Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/), se ainda não o fez. Depois disso, execute o seguinte cmdlet para iniciar scontabilidade na sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>Crie a sua piscina de anfitriões

Pode criar uma piscina de anfitriões seguindo as instruções em qualquer um destes artigos:
- [Tutorial: Criar uma piscina de anfitriões com o Azure Marketplace](create-host-pools-azure-marketplace-2019.md)
- [Criar um conjunto de anfitriões com um modelo do Azure Resource Manager](create-host-pools-arm-template.md)
- [Criar um conjunto de anfitriões com o PowerShell](create-host-pools-powershell-2019.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Defina a sua piscina de anfitriões como uma piscina de anfitriões de validação

Executar os seguintes cmdlets PowerShell para definir a nova piscina hospedeira como uma piscina de anfitrião de validação. Substitua os valores em ações pelos valores relevantes para a sua sessão:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Executar o cmdlet PowerShell seguinte para confirmar que a propriedade de validação foi definida. Substitua os valores em ações pelos valores relevantes para a sua sessão.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool"
```

Os resultados do cmdlet devem ser semelhantes a esta saída:

```
    TenantName          : contoso
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False
    CustomRdpProperty    : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Agenda de atualização

As atualizações de serviço acontecem mensalmente. Se houver problemas importantes, as atualizações críticas serão fornecidas a um ritmo mais frequente.

## <a name="next-steps"></a>Passos seguintes

Agora que criou um pool de anfitriões de validação, pode aprender a usar o Azure Service Health para monitorizar a sua implementação virtual do Windows Desktop.

> [!div class="nextstepaction"]
> [Configurar alertas de serviço](set-up-service-alerts-2019.md)
