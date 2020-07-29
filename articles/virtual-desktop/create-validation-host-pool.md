---
title: Atualizações do serviço de piscina do anfitrião virtual do Windows Desktop - Azure
description: Como criar um pool de anfitriões de validação para monitorizar as atualizações do serviço antes de lançar atualizações para a produção.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 177763e7939de082faa0c83d2ab661292f0758b2
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292654"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Tutorial: Criar uma piscina de anfitriões para validar atualizações de serviço

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md).

As piscinas hospedeiras são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de inquilinos do Windows Virtual Desktop. Antes de colocar piscinas hospedeiras no seu ambiente de produção, recomendamos vivamente que crie uma piscina de anfitriões de validação. As atualizações são aplicadas primeiro aos pools de anfitriões de validação, permitindo-lhe monitorizar as atualizações de serviço antes de as lançar para o seu ambiente de produção. Sem um pool de anfitriões de validação, pode não descobrir alterações que introduzam erros, o que pode resultar em tempo de inatividade para os utilizadores no seu ambiente de produção.

Para garantir que as suas aplicações funcionam com as últimas atualizações, o pool de anfitriões de validação deve ser o mais similar aos pools de anfitriões no seu ambiente de produção. Os utilizadores devem ligar-se com tanta frequência à piscina do anfitrião de validação como fazem com a piscina de anfitriões de produção. Se tiver testes automatizados na sua piscina de anfitriões, deverá incluir testes automatizados na piscina do anfitrião de validação.

Pode depurar problemas no pool do anfitrião de validação com [a funcionalidade de diagnóstico](diagnostics-role-service.md) ou com os [artigos de resolução de problemas do Windows Virtual Desktop](troubleshoot-set-up-overview.md).

>[!NOTE]
> Recomendamos que deixe a piscina de anfitriões de validação no lugar para testar todas as atualizações futuras.

>[!IMPORTANT]
>O Windows Virtual Desktop com a integração de Gestão de Recursos Azure tem atualmente dificuldades em permitir e desativar ambientes de validação. Atualizaremos este artigo quando resolvermos o problema.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, siga as instruções em [Configurar o módulo Virtual Desktop PowerShell](powershell-module.md) para configurar o módulo PowerShell e iniciar o súm na Azure.

## <a name="create-your-host-pool"></a>Crie a sua piscina de anfitriões

Pode criar uma piscina de anfitriões seguindo as instruções em qualquer um destes artigos:
- [Tutorial: Criar uma piscina de anfitriões com o Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Criar um conjunto de anfitriões com o PowerShell](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Defina a sua piscina de anfitriões como uma piscina de anfitriões de validação

Executar os seguintes cmdlets PowerShell para definir a nova piscina hospedeira como uma piscina de anfitrião de validação. Substitua os valores nos parênteses pelos valores relevantes para a sua sessão:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -ValidationEnvironment:$true
```

Executar o cmdlet PowerShell seguinte para confirmar que a propriedade de validação foi definida. Substitua os valores nos parênteses pelos valores relevantes para a sua sessão.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | Format-List
```

Os resultados do cmdlet devem ser semelhantes a esta saída:

```powershell
    HostPoolName        : hostpoolname
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnvironment : True
```

## <a name="update-schedule"></a>Agenda de atualização

As atualizações de serviço acontecem mensalmente. Se houver problemas importantes, as atualizações críticas serão fornecidas a um ritmo mais frequente.

Se houver alguma atualização de serviço, certifique-se de que tem pelo menos um pequeno grupo de utilizadores a assinar todos os dias para validar o ambiente. Recomendamos que visite regularmente o nosso [site TechCommunity](https://techcommunity.microsoft.com/t5/forums/searchpage/tab/message?filter=location&q=wvdupdate&location=forum-board:WindowsVirtualDesktop&sort_by=-topicPostDate&collapse_discussion=true) e siga quaisquer posts com a WVDUPdate para se manter informado sobre as atualizações de serviço.

## <a name="next-steps"></a>Passos seguintes

Agora que criou um pool de anfitriões de validação, pode aprender a usar o Azure Service Health para monitorizar a sua implementação virtual do Windows Desktop. 

> [!div class="nextstepaction"]
> [Configurar alertas de serviço](./set-up-service-alerts.md)
