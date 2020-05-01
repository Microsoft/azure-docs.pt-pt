---
title: Atualizações do serviço de piscina do Windows Virtual Desktop - Azure
description: Como criar um pool de anfitriões de validação para monitorizar as atualizações de serviço antes de lançar atualizações para a produção.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b74b7f0b79ad4064d7133a19316d6aec6bd5ba3a
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611573"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Tutorial: Criar um pool de anfitriões para validar atualizações de serviço

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização da primavera de 2020 com os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows. Se estiver a utilizar o lançamento do Windows Virtual Desktop Fall 2019 sem objetos do Gestor de Recursos Azure, consulte [este artigo](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md).
>
> A atualização Do Windows Virtual Desktop Spring 2020 encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-la para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

As piscinas hospedeiras são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de inquilinos do Windows Virtual Desktop. Antes de implementar piscinas hospedeiras para o seu ambiente de produção, recomendamos vivamente que crie um pool de anfitriões de validação. As atualizações são aplicadas primeiro às piscinas de anfitriões de validação, permitindo-lhe monitorizar as atualizações de serviço antes de as lançar para o seu ambiente de produção. Sem um pool de hospedagem de validação, não poderá descobrir alterações que introduzam erros, o que pode resultar em tempo de inatividade para os utilizadores no seu ambiente de produção.

Para garantir que as suas aplicações funcionam com as mais recentes atualizações, o pool anfitrião de validação deve ser o mais semelhante possível às piscinas hospedadas no seu ambiente de produção. Os utilizadores devem ligar-se com a mesma frequência à piscina anfitriã da validação como fazem com a piscina de anfitriões de produção. Se tiver testes automatizados na sua piscina de anfitriões, deverá incluir testes automatizados na piscina de anfitriões de validação.

Pode desinventar problemas no conjunto de anfitriões de validação com a funcionalidade de diagnóstico ou os [artigos](diagnostics-role-service.md) de resolução de [problemas do Windows Virtual Desktop.](troubleshoot-set-up-overview.md)

>[!NOTE]
> Recomendamos que deixe o conjunto de anfitriões de validação no lugar para testar todas as atualizações futuras.

>[!IMPORTANT]
>O lançamento do Windows Virtual Desktop Spring 2020 tem atualmente problemas em permitir e desativar o ambiente de validação. Atualizaremos este artigo quando resolvermos o problema.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, siga as instruções no [Conjunto do módulo PowerShell virtual do Windows](powershell-module.md) para configurar o módulo PowerShell e iniciar o seu início no Azure.

## <a name="create-your-host-pool"></a>Crie a sua piscina de anfitrião

Pode criar uma piscina de anfitriões seguindo as instruções em qualquer um destes artigos:
- [Tutorial: Crie uma piscina de anfitriões com o Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Criar um conjunto de anfitriões com o PowerShell](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Defina a sua piscina anfitriã como uma piscina de anfitriões de validação

Execute os seguintes cmdlets PowerShell para definir a nova piscina anfitriã como uma piscina anfitriã de validação. Substitua os valores em parênteses com os valores relevantes para a sua sessão:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -ValidationEnvironment:$true
```

Execute o seguinte cmdlet PowerShell para confirmar que a propriedade de validação foi definida. Substitua os valores em parênteses com os valores relevantes para a sua sessão.

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

## <a name="update-schedule"></a>Calendário de atualização

As atualizações de serviço acontecem mensalmente. Se houver grandes problemas, as atualizações críticas serão fornecidas a um ritmo mais frequente.

Se houver alguma atualização de serviço, certifique-se de que tem pelo menos um pequeno grupo de utilizadores que se inscrevem todos os dias para validar o ambiente. Recomendamos que visite regularmente o nosso [site TechCommunity](https://techcommunity.microsoft.com/t5/forums/searchpage/tab/message?filter=location&q=wvdupdate&location=forum-board:WindowsVirtualDesktop&sort_by=-topicPostDate&collapse_discussion=true) e siga quaisquer publicações com WVDUPdate para se manter informado sobre atualizações de serviço.

## <a name="next-steps"></a>Passos seguintes

Agora que criou um conjunto de anfitriões de validação, pode aprender a usar a Azure Service Health para monitorizar a implementação do Windows Virtual Desktop. 

> [!div class="nextstepaction"]
> [Configurar alertas de serviço](./set-up-service-alerts.md)
