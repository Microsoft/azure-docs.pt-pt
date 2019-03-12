---
title: Scripts do PowerShell com o módulo de Az.Search - Azure Search
description: Criar e configurar um serviço Azure Search com o PowerShell. Pode dimensionar um serviço ou reduzir verticalmente, gerir a administração e chaves de api de consulta e informações do sistema de consulta.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: heidist
ms.openlocfilehash: 541feee2005428226b3f46927bc0e4bfb53cc98d
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57781720"
---
# <a name="manage-your-azure-search-service-with-powershell"></a>Gerir o seu serviço de Azure Search com o PowerShell
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [API REST](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Pode executar cmdlets do PowerShell e scripts no Windows, Linux, ou num [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) para criar e configurar [Azure Search](https://docs.microsoft.com/azure/search/). O [ **Az.Search** ](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) estende o módulo [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.4.0) com paridade completa para o [APIs de REST de gestão do Azure Search](https://docs.microsoft.com/rest/api/searchmanagement). Com o Azure PowerShell e **Az.Search**, pode realizar as seguintes tarefas:

> [!div class="checklist"]
> * [Listar todos os serviços de pesquisa na sua subscrição](#list-search-services)
> * [Obter informações sobre um serviço de pesquisa específica](#get-search-service-information)
> * [Criar ou eliminar um serviço](#create-or-delete-a-service)
> * [Administrador de voltar a gerar as chaves de API](#regenerate-admin-api-keys)
> * [Criar ou eliminar as chaves de api de consulta](#create-or-delete-query-keys)
> * [Dimensionar um serviço ao aumentar ou diminuir as réplicas e partições](#scale-replicas-and-partitions)

Não é possível utilizar o PowerShell para alterar o nome, a região ou o escalão do seu serviço. Recursos dedicados são alocados quando é criado um serviço. Alteração do hardware subjacente (tipo de nó ou de localização) requer um novo serviço. Não há ferramentas ou APIs para a transferência de conteúdo. Toda a gestão de conteúdo é através de [REST](https://docs.microsoft.com/rest/api/searchservice/) ou [.NET](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search) APIs, e se quiser mover índices, terá de recriar e recarregá-los num novo serviço. 

Embora não haja nenhum comandos do PowerShell dedicados para gestão de conteúdos, pode escrever o script do PowerShell que chama REST ou do .NET para criar e carregar índices. O **Az.Search** módulo por si só não fornece estas operações.

Outras tarefas não é suportadas através do PowerShell ou qualquer outra API (só de portal) incluem:
+ [Anexar um recurso dos serviços cognitivos](cognitive-search-attach-cognitive-services.md) para [indexação enriquecida de IA](cognitive-search-concept-intro.md). Um serviço cognitivo está ligado a um conjunto de capacidades, não uma subscrição ou serviço.
+ [Soluções de monitorização de suplemento](search-monitor-usage.md#add-on-monitoring-solutions) ou [análise de tráfego de pesquisa](search-traffic-analytics.md) utilizado para monitorizar o Azure Search.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Verifique as versões e módulos de carga

Os exemplos neste artigo são interativos e requerem permissões elevadas. O Azure PowerShell (a **Az** módulo) tem de ser instalado. Para obter mais informações, consulte [instalar o Azure PowerShell](/powershell/azure/overview).

### <a name="powershell-version-check-51-or-later"></a>Verificação da versão do PowerShell (5.1 ou posterior)

Local PowerShell tem de ser 5.1 ou posterior, em qualquer sistema operacional compatível.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Carregar o Azure PowerShell

Se não tiver a certeza se **Az** estiver instalado, execute o comando seguinte como passo de verificação. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Alguns sistemas fazem não automaticamente carregar módulos. Se obtiver um erro no comando anterior, tente carregar o módulo e se isso falhar, voltar para as instruções de instalação para ver se perdeu uma etapa.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Ligar ao Azure com um token de início de sessão no browser

Pode utilizar as credenciais de início de sessão no portais para ligar a uma subscrição no PowerShell. Em alternativa, pode [autenticar de forma não interativa com um principal de serviço](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

```azurepowershell-interactive
Connect-AzAccount
```

Se armazenar várias subscrições do Azure, defina a sua subscrição do Azure. Para ver uma lista das suas subscrições atuais, execute este comando.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Para especificar a subscrição, execute o seguinte comando. No exemplo a seguir, é o nome da subscrição `ContosoSubscription`.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-all-azure-search-services-in-your-subscription"></a>Listar todos os serviços do Azure Search na sua subscrição

Os comandos seguintes são partir [ **Az.Resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources), retornar informações sobre os recursos existentes e os serviços que já são aprovisionados na sua subscrição. Se não souber o número de serviços de pesquisa já são criados, estes comandos retornam essas informações, economizando uma viagem para o portal.

O primeiro comando devolve todos os serviços de pesquisa.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Na lista de serviços, devolva informações sobre um recurso específico.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Resultados devem ser semelhantes à saída seguinte.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Importar Az.Search

Os comandos do [ **Az.Search** ](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) não estão disponíveis até carregar o módulo.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Lista de todos os comandos de Az.Search

Como passo de verificação, retorna uma lista de comandos fornecidos no módulo.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Resultados devem ser semelhantes à saída seguinte.

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

## <a name="get-search-service-information"></a>Obter informações do serviço de pesquisa

Após **Az.Search** é importado e sabe que o grupo de recursos que contém o serviço de pesquisa, execute [Get-AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) para retornar a definição de serviço, incluindo o nome, região, camada e réplica e contagens de partição.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Resultados devem ser semelhantes à saída seguinte.

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

[**Novo AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) é utilizado para [criar um novo serviço de pesquisa](search-create-service-portal.md).

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
Resultados devem ser semelhantes à saída seguinte.

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

## <a name="regenerate-admin-keys"></a>Regenerar chaves de administração

[**Novo AzSearchAdminKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) é utilizado para o rollover administrador [chaves de API](search-security-api-keys.md). Duas chaves administrativas são criadas com cada serviço de acesso autenticado. As chaves são necessárias em cada solicitação. Ambas as chaves de administração são funcionalmente equivalentes, conceder acesso de escrita total para um serviço de pesquisa com a capacidade de recuperar quaisquer informações ou criar e eliminar qualquer objeto. Duas chaves existem para que pode usar uma ao substituir o outro. 

Pode apenas voltar a gerar um de cada vez, especificado como o `primary` ou `secondary` chave. Para o serviço ininterrupto, lembre-se atualizar todo o código de cliente para utilizar uma chave secundária ao disponíveis na chave primária. Evite alterar as chaves enquanto operações estão em trânsito.

Como pode esperar, se voltar a gerar chaves sem atualizar o código de cliente, irão falhar pedidos de utilizar a chave antiga. Voltar a gerar novas chaves de todos os não obrigá-lo fora do seu serviço e, ainda pode aceder ao serviço através do portal. Depois de regenerar chaves primárias e secundárias, pode atualizar o código de cliente para utilizar as novas chaves e operações serão retomada em conformidade.

Valores para as chaves de API são gerados pelo serviço. Não é possível fornecer uma chave personalizada para o Azure Search utilizar. Da mesma forma, não há nenhum nome definido pelo utilizador para as chaves de API de administrador. Referências para a chave são fixos cadeias de caracteres, seja `primary` ou `secondary`. 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Resultados devem ser semelhantes à saída seguinte. Ambas as chaves são devolvidas, mesmo que apenas alterar um de cada vez.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Criar ou eliminar as chaves de consulta

[**Novo AzSearchQueryKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) é utilizado para criar consulta [chaves de API](search-security-api-keys.md) para acesso só de leitura a partir de aplicações de cliente para um índice da Azure Search. Chaves de consulta são utilizadas para autenticar para um índice específico com o objetivo de obter os resultados da pesquisa. Chaves de consulta não conceda acesso só de leitura a outros itens no serviço, como um índice, a origem de dados ou o indexador.

Não é possível fornecer uma chave para o Azure Search utilizar. Chaves de API são geradas pelo serviço.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Dimensionamento réplicas e partições

[**Conjunto AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) é utilizado para [aumentar ou diminuir as réplicas e partições](search-capacity-planning.md) de reajustar os recursos faturáveis dentro do seu serviço. Aumentar as réplicas ou partições adiciona à sua fatura, que tem ambos os custos fixos e variáveis. Se tiver uma necessidade temporária de potência de processamento adicional, pode aumentar as réplicas e partições para processar a carga de trabalho. A área de monitorização na página Descrição geral do portal tem mosaicos na latência da consulta, consultas por segundo e limitação, que indica se a capacidade atual é adequada.

Pode demorar algum tempo para adicionar ou remover a obtenção de recursos. Ajustes à capacidade ocorrerem em segundo plano, que permite que cargas de trabalho existentes continuar. Capacidade adicional é usada para solicitações de entrada, assim que estiver pronto, sem qualquer configuração adicional necessária. 

Remover capacidade pode ser prejudicais. A parar todas as tarefas de indexação e indexador antes reduzindo a capacidade é recomendada para evitar pedidos ignorados. Se isso não é viável, pode considerar reduzindo a capacidade de forma incremental, uma réplica e partição por vez, até que os novos níveis de destino são atingidos.

Depois de submeter o comando, não é possível encerrá-lo enquanto. Terá de aguardar até que o comando é concluído antes de rever as contagens.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Resultados devem ser semelhantes à saída seguinte.

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


## <a name="next-steps"></a>Passos Seguintes

Criar uma [índice](search-what-is-an-index.md), [consultar um índice](search-query-overview.md) com o portal, REST APIs ou o SDK do .NET.

* [Criar um índice da Azure Search no portal do Azure](search-create-index-portal.md)
* [Configurar um indexador para carregar dados de outros serviços](search-indexer-overview.md)
* [Consultar um índice da Azure Search utilizando o Explorador de pesquisa no portal do Azure](search-explorer.md)
* [Como utilizar o Azure Search no .NET](search-howto-dotnet-sdk.md)