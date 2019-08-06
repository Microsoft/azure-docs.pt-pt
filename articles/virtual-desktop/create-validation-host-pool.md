---
title: Criar um pool de hosts da visualização de área de trabalho virtual do Windows para validar atualizações de serviço – Azure
description: Como criar um pool de hosts de validação para monitorar atualizações de serviço antes de distribuir as atualizações para produção.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/08/2019
ms.author: helohr
ms.openlocfilehash: a23d9fe932556b0a685b373b060901d2c7fb8c85
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816452"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Tutorial: Criar um conjunto de anfitriões para validar as atualizações de serviço

Os pools de hosts são uma coleção de uma ou mais máquinas virtuais idênticas nos ambientes de locatário da visualização de área de trabalho virtual do Windows. Antes de implantar pools de hosts em seu ambiente de produção, é altamente recomendável criar um pool de hosts de validação. As atualizações são aplicadas primeiro aos pools de hosts de validação, permitindo que você monitore as atualizações de serviço antes de distribuí-las para o ambiente de produção. Sem um pool de hosts de validação, você não pode descobrir alterações que introduzam erros, o que pode resultar em tempo de inatividade para os usuários em seu ambiente de produção.

Para garantir que seus aplicativos funcionem com as atualizações mais recentes, o pool de hosts de validação deve ser semelhante aos pools de hosts em seu ambiente de produção o mais recente possível. Os usuários devem se conectar com frequência ao pool de hosts de validação, como no pool de hosts de produção. Se você tiver testes automatizados em seu pool de hosts, deverá incluir testes automatizados no pool de hosts de validação.

Você pode depurar problemas no pool de hosts de validação com [o recurso de diagnóstico](diagnostics-role-service.md) ou com os [artigos de solução de problemas de área de trabalho virtual do Windows](https://docs.microsoft.com/Azure/virtual-desktop/troubleshoot-set-up-overview).

>[!NOTE]
> Recomendamos que você deixe o pool de hosts de validação em vigor para testar todas as atualizações futuras.

Antes de começar, [Baixe e importe o módulo do PowerShell da área de trabalho virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview), caso ainda não tenha feito isso.

## <a name="create-your-host-pool"></a>Criar seu pool de hosts

Você pode criar um pool de hosts seguindo as instruções em qualquer um destes artigos:
- [Tutorial: Criar um pool de hosts com o Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Criar um pool de hosts com um modelo de Azure Resource Manager](create-host-pools-arm-template.md)
- [Criar um pool de hosts com o PowerShell](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Definir o pool de hosts como um pool de hosts de validação

Execute os seguintes cmdlets do PowerShell para definir o novo pool de hosts como um pool de hosts de validação. Substitua os valores entre aspas pelos valores relevantes para sua sessão:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Execute o seguinte cmdlet do PowerShell para confirmar que a propriedade de validação foi definida. Substitua os valores entre aspas pelos valores relevantes para sua sessão.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Os resultados do cmdlet devem ser semelhantes a esta saída:

```
    TenantName          : contoso 
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Agenda de atualização

Na visualização, as atualizações de serviço ocorrem em aproximadamente uma cadência mensal. Se houver problemas importantes, as atualizações críticas serão fornecidas em uma cadência mais frequente.

## <a name="next-steps"></a>Passos Seguintes

Agora que você criou um pool de hosts de validação, você pode aprender a implantar e conectar-se a uma ferramenta de gerenciamento para gerenciar recursos da área de trabalho virtual da Microsoft.

> [!div class="nextstepaction"]
> [Tutorial de implantação de uma ferramenta de gerenciamento](./manage-resources-using-ui.md)
