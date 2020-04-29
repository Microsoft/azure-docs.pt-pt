---
title: Scripts PowerShell usando módulo Az.Search
titleSuffix: Azure Cognitive Search
description: Crie e configure um serviço de pesquisa cognitiva Azure com powerShell. Pode escalar um serviço para cima ou para baixo, gerir a administração e consultar as teclas api e consultar informações sobre o sistema.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 711071e08a52a0075512bc8b3ffe14707238cdfe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77209301"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>Gerencie o seu serviço de pesquisa cognitiva Azure com a PowerShell
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [API REST](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Pitão](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Pode executar cmdlets e scripts PowerShell no Windows, Linux ou em [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) para criar e configurar a Pesquisa Cognitiva Azure. O módulo **Az.Search** estende o [Azure PowerShell](https://docs.microsoft.com/powershell/) com total paridade às APIs REST de [Gestão](https://docs.microsoft.com/rest/api/searchmanagement) de Pesquisa e a capacidade de executar as seguintes tarefas:

> [!div class="checklist"]
> * [Listar serviços de pesquisa numa subscrição](#list-search-services)
> * [Informações de serviço de devolução](#get-search-service-information)
> * [Criar ou eliminar um serviço](#create-or-delete-a-service)
> * [Chaves API de administração regenerada](#regenerate-admin-keys)
> * [Criar ou eliminar as teclas de api-perguntas](#create-or-delete-query-keys)
> * [Escala para cima ou para baixo com réplicas e divisórias](#scale-replicas-and-partitions)

Ocasionalmente, fazem-se perguntas sobre tarefas *que não* constam da lista acima. Atualmente, não é possível utilizar o módulo **Az.Search** ou a gestão REST API para alterar um nome, região ou nível de servidor. Os recursos dedicados são atribuídos quando um serviço é criado. Como tal, a alteração do hardware subjacente (localização ou tipo de nó) requer um novo serviço. Da mesma forma, não existem ferramentas ou APIs para transferir conteúdo, como um índice, de um serviço para outro.

Dentro de um serviço, a criação e gestão de conteúdos é através do Serviço de [Pesquisa REST API](https://docs.microsoft.com/rest/api/searchservice/) ou [.NET SDK](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search). Embora não existam comandos PowerShell dedicados para conteúdos, pode escrever o script PowerShell que chama APIs REST ou .NET para criar e carregar índices.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Verifique versões e módulos de carga

Os exemplos deste artigo são interativos e requerem permissões elevadas. O Azure PowerShell (módulo **Az)** deve ser instalado. Para mais informações, consulte [Instalar o Azure PowerShell](/powershell/azure/overview).

### <a name="powershell-version-check-51-or-later"></a>Verificação da versão PowerShell (5.1 ou mais tarde)

A PowerShell local deve ser 5.1 ou mais tarde, em qualquer sistema operativo suportado.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Carregar PowerShell Azure

Se não tiver a certeza se **a Az** está instalada, execute o seguinte comando como passo de verificação. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Alguns sistemas não carregam automaticamente os módulos. Se tiver um erro no comando anterior, tente carregar o módulo e, se isso falhar, volte às instruções de instalação para ver se perdeu um passo.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Ligue-se ao Azure com um sinal de entrada de navegador

Pode utilizar as credenciais de acesso ao portal para se ligar a uma subscrição no PowerShell. Alternativamente, pode [autenticar não interactivamente com um diretor de serviço](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

```azurepowershell-interactive
Connect-AzAccount
```

Se tiver várias subscrições do Azure, detete a sua subscrição Azure. Para ver uma lista das suas subscrições atuais, execute este comando.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Para especificar a subscrição, execute o seguinte comando. No exemplo seguinte, o `ContosoSubscription`nome da subscrição é .

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Listar serviços numa subscrição

Os seguintes comandos são da [**Az.Resources,**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources)devolvendo informações sobre os recursos e serviços existentes já prestados na sua subscrição. Se não sabe quantos serviços de pesquisa já foram criados, estes comandos devolvem essa informação, poupando-lhe uma viagem ao portal.

O primeiro comando devolve todos os serviços de pesquisa.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Da lista de serviços, devolva informações sobre um recurso específico.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Os resultados devem ser semelhantes aos seguintes resultados.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Import Az.Search

Os comandos da [**Az.Search**](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) não estão disponíveis até carregar o módulo.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Listar todos os comandos De Pesquisa Az.Search

Como passo de verificação, devolva uma lista de comandos fornecidos no módulo.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Os resultados devem ser semelhantes aos seguintes resultados.

```
CommandType     Name                                Version    Source
-----------     ----                                -------    ------
Cmdlet          Get-AzSearchAdminKeyPair            0.7.1      Az.Search
Cmdlet          Get-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          Get-AzSearchService                 0.7.1      Az.Search
Cmdlet          New-AzSearchAdminKey                0.7.1      Az.Search
Cmdlet          New-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          New-AzSearchService                 0.7.1      Az.Search
Cmdlet          Remove-AzSearchQueryKey             0.7.1      Az.Search
Cmdlet          Remove-AzSearchService              0.7.1      Az.Search
Cmdlet          Set-AzSearchService                 0.7.1      Az.Search
```

## <a name="get-search-service-information"></a>Obtenha informações sobre serviço de pesquisa

Depois da **Az.Search** ser importada e conhecer o grupo de recursos que contém o seu serviço de pesquisa, executar [O Get-AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) para devolver a definição de serviço, incluindo nome, região, nível e réplica e partilha.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Os resultados devem ser semelhantes aos seguintes resultados.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>Criar ou eliminar um serviço

[**O New-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) é utilizado para criar um novo serviço de [pesquisa.](search-create-service-portal.md)

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
Os resultados devem ser semelhantes aos seguintes resultados.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

## <a name="regenerate-admin-keys"></a>Chaves de administração regeneradas

[**New-AzSearchAdminKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) é usado para rolar sobre [as teclas API](search-security-api-keys.md)de administrador . São criadas duas chaves de administração com cada serviço para acesso autenticado. As chaves são necessárias em todos os pedidos. Ambas as teclas de administração são funcionalmente equivalentes, concedendo acesso por escrito completo a um serviço de pesquisa com a capacidade de recuperar qualquer informação, ou criar e eliminar qualquer objeto. Existem duas teclas para que possa usar uma enquanto substitui a outra. 

Só se pode regenerar um de cada `primary` vez, especificado como a ou `secondary` a chave. Para um serviço ininterrupto, lembre-se de atualizar todo o código do cliente para utilizar uma chave secundária enquanto passa a tecla primária. Evite mudar as teclas durante o voo.

Como seria de esperar, se regenerar as chaves sem atualizar o código do cliente, os pedidos que utilizam a tecla antiga falharão. Regenerar todas as chaves novas não o bloqueia permanentemente fora do seu serviço, podendo ainda aceder ao serviço através do portal. Depois de regenerar as teclas primárias e secundárias, pode atualizar o código do cliente para utilizar as novas teclas e as operações serão retomadas em conformidade.

Os valores das teclas API são gerados pelo serviço. Não é possível fornecer uma chave personalizada para a pesquisa cognitiva azure para usar. Da mesma forma, não existe um nome definido pelo utilizador para as teclas API do administrador. As referências à chave são `primary` cordas `secondary`fixas, quer quer. 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Os resultados devem ser semelhantes aos seguintes resultados. Ambas as chaves são devolvidas, mesmo que só mude uma de cada vez.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Criar ou eliminar chaves de consulta

[**O New-AzSearchQueryKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) é usado para criar [chaves API](search-security-api-keys.md) de consulta para acesso apenas de leitura de aplicações de clientes a um índice de Pesquisa Cognitiva Azure. As chaves de consulta são usadas para autenticar um índice específico com o propósito de recuperar resultados de pesquisa. As teclas de consulta não concedem acesso apenas a leitura a outros itens do serviço, tais como um índice, fonte de dados ou indexador.

Não é possível fornecer uma chave para a pesquisa cognitiva azure usar. As teclas API são geradas pelo serviço.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Réplicas e divisórias em escala

[**O Set-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) é usado para [aumentar ou diminuir réplicas e divisórias](search-capacity-planning.md) para reajustar recursos faturados dentro do seu serviço. O aumento de réplicas ou divisórias adiciona à sua conta, que tem encargos fixos e variáveis. Se tiver uma necessidade temporária de poder de processamento adicional, pode aumentar réplicas e divisórias para lidar com a carga de trabalho. A área de monitorização na página do portal Overview tem azulejos sobre latência de consultas, consultas por segundo, e estrangulamento, indicando se a capacidade atual é adequada.

Pode demorar um pouco a adicionar ou remover o reabastecimento. Os ajustes na capacidade ocorrem em segundo plano, permitindo que as cargas de trabalho existentes continuem. A capacidade adicional é usada para pedidos de entrada assim que estiver pronto, sem necessidade de configuração adicional. 

Remover a capacidade pode ser perturbador. Recomenda-se a paragem de todos os postos de trabalho indexados e indexantes antes da redução da capacidade para evitar pedidos de queda. Se isso não for viável, poderá considerar a redução incremental da capacidade, uma réplica e partição de cada vez, até que os seus novos níveis de alvo sejam atingidos.

Uma vez que submeta o comando, não há como terminá-lo a meio. Terá que esperar até que o comando termine antes de rever as contagens.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Os resultados devem ser semelhantes aos seguintes resultados.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="next-steps"></a>Passos seguintes

Construa um [índice,](search-what-is-an-index.md) [consulta um índice](search-query-overview.md) utilizando o portal, ASAP REST ou o .NET SDK.

* [Crie um índice de pesquisa cognitiva Azure no portal Azure](search-create-index-portal.md)
* [Configurar um indexante para carregar dados de outros serviços](search-indexer-overview.md)
* [Consulta um índice de pesquisa cognitiva Azure usando explorador de pesquisa no portal Azure](search-explorer.md)
* [Como utilizar a Pesquisa Cognitiva Azure em .NET](search-howto-dotnet-sdk.md)