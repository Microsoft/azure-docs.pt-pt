---
title: Windows Virtual Desktop host pool service updates (outono 2019) - Azure
description: Aprenda a criar um pool de anfitriões de validação para monitorizar as atualizações do serviço antes de lançar atualizações para a produção.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 806c3396d9188ea6abc5f779a26d99247d802ebe
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527595"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates-fall-2019-release"></a>Tutorial: Criar uma piscina de anfitriões para validar atualizações de serviço (lançamento outono 2019)

>[!IMPORTANT]
>Este conteúdo aplica-se à versão outono 2019 que não suporta objetos de ambiente de trabalho virtuais do Gestor de Recursos Azure. Se está a tentar gerir os objetos virtuais do Azure Resource Manager Windows, introduzidos na atualização da primavera de 2020, consulte [este artigo](../create-validation-host-pool.md).

As piscinas hospedeiras são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de inquilinos do Windows Virtual Desktop. Antes de colocar piscinas hospedeiras no seu ambiente de produção, recomendamos vivamente que crie uma piscina de anfitriões de validação. As atualizações são aplicadas primeiro aos pools de anfitriões de validação, permitindo-lhe monitorizar as atualizações de serviço antes de as lançar para o seu ambiente de produção. Sem um pool de anfitriões de validação, pode não descobrir alterações que introduzam erros, o que pode resultar em tempo de inatividade para os utilizadores no seu ambiente de produção.

Para garantir que as suas aplicações funcionam com as últimas atualizações, o pool de anfitriões de validação deve ser o mais similar aos pools de anfitriões no seu ambiente de produção. Os utilizadores devem ligar-se com tanta frequência à piscina do anfitrião de validação como fazem com a piscina de anfitriões de produção. Se tiver testes automatizados na sua piscina de anfitriões, deverá incluir testes automatizados na piscina do anfitrião de validação.

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

## <a name="next-steps"></a>Próximos passos

Agora que criou um pool de anfitriões de validação, pode aprender a usar o Azure Service Health para monitorizar a sua implementação virtual do Windows Desktop. 

> [!div class="nextstepaction"]
> [Configurar alertas de serviço](set-up-service-alerts-2019.md)
