---
title: Scripts Azure CLI usando módulo de pesquisa az
titleSuffix: Azure Cognitive Search
description: Criar e configurar um serviço de Pesquisa Cognitiva Azure com o Azure CLI. Pode escalar um serviço para cima ou para baixo, gerir a administração e consultar as teclas api e consultar informações do sistema.
manager: luisca
author: DerekLegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: bb132baf3e93242c1b228f0ac7f0cfb7f41e1164
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680374"
---
# <a name="manage-your-azure-cognitive-search-service-with-the-azure-cli"></a>Gerencie o seu serviço de Pesquisa Cognitiva Azure com o Azure CLI
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [CLI do Azure](search-manage-azure-cli.md)
> * [API REST](/rest/api/searchmanagement/)
> * [SDK do .NET](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

Pode executar comandos e scripts Azure CLI no Windows, macOS, Linux ou em [Azure Cloud Shell](../cloud-shell/overview.md) para criar e configurar a Azure Cognitive Search. O módulo [**de pesquisa az**](/cli/azure/search) estende o [CLI Azure](/cli/) com total paridade às APIs de [Gestão de Gestão de Pesquisa](/rest/api/searchmanagement) e a capacidade de executar as seguintes tarefas:

> [!div class="checklist"]
> * [Listar serviços de pesquisa em uma subscrição](#list-search-services)
> * [Informações sobre serviços de devolução](#get-search-service-information)
> * [Criar ou apagar um serviço](#create-or-delete-a-service)
> * [Criar um serviço com um ponto final privado](#create-a-service-with-a-private-endpoint)
> * [Regenerar a admin API-keys](#regenerate-admin-keys)
> * [Criar ou eliminar chaves api de consulta](#create-or-delete-query-keys)
> * [Escalar para cima ou para baixo com réplicas e divisórias](#scale-replicas-and-partitions)
> * [Criar um recurso de ligação privada partilhada](#create-a-shared-private-link-resource)

Ocasionalmente, fazem-se perguntas sobre tarefas *que não* constam da lista acima. Atualmente, não é possível utilizar o módulo **de pesquisa az** ou a gestão REST API para alterar um nome de servidor, região ou nível. Os recursos dedicados são atribuídos quando um serviço é criado. Como tal, a alteração do hardware subjacente (localização ou tipo de nó) requer um novo serviço. Da mesma forma, não existem ferramentas ou APIs para transferir conteúdo, como um índice, de um serviço para outro.

Dentro de um serviço, a criação e gestão de conteúdos são através [do Serviço de Busca REST API](/rest/api/searchservice/) ou [.NET SDK](/dotnet/api/overview/azure/search.documents-readme). Embora não existam comandos dedicados do PowerShell para conteúdos, pode escrever scripts que liguem para REST ou .NET APIs para criar e carregar índices.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-upgrade"></a>Verifique versões e upgrade

Os exemplos deste artigo são interativos e requerem permissões elevadas. O Azure CLI deve ser instalado. Para mais informações, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

Agora pode executar o CLI Azure com o `az` comando de Orte de Comando do Windows, PowerShell ou [Azure Cloud Shell](../cloud-shell/overview). O PowerShell fornece algumas funcionalidades de conclusão de tabulação que não estão disponíveis a partir da Linha de Comandos do Windows. 

### <a name="check-the-azure-cli-version"></a>Consulte a versão Azure CLI

Se não tiver a certeza se o Azure CLI está instalado, executar o seguinte comando como passo de verificação. 

```azurecli-interactive
az --version
```
Se este comando não funcionar, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli) para instalar o Azure CLI.

Se tiver a versão 2.11.0 ou mais recente, pode executar o `az upgrade` comando para atualizar o CLI para a versão mais recente.

```azurecli-interactive
az upgrade
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Conecte-se ao Azure com um token de inscrição no navegador

Pode utilizar as suas credenciais de inscrição no portal para se ligar a uma subscrição no CLI Azure. Em alternativa, pode [autenticar não interativamente com um diretor de serviço.](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal)

```azurecli-interactive
az login
```

Se tiver várias subscrições do Azure, detenha a sua subscrição Azure. Para ver uma lista das suas subscrições atuais, execute este comando.

```azurecli-interactive
az account list --output table
```

Para especificar a subscrição, executar o seguinte comando. No exemplo seguinte, o nome de subscrição é `ContosoSubscription` .

```azurecli-interactive
az account set --subscription "ContosoSubscription"
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Listar serviços numa subscrição

Os seguintes comandos são de [**recurso az,**](/cli/azure/resource)devolvendo informações sobre os recursos e serviços já prestados na sua subscrição. Se não sabe quantos serviços de pesquisa já foram criados, estes comandos devolvem essa informação, poupando-lhe uma viagem ao portal.

O primeiro comando devolve todos os serviços de pesquisa.

```azurecli-interactive
az resource list --resource-type Microsoft.Search/searchServices --output table
```

Da lista de serviços, devolva informações sobre um recurso específico.

```azurecli-interactive
az resource list --name <service-name>
```

## <a name="list-all-az-search-commands"></a>Listar todos os comandos de pesquisa az

Pode ver informações sobre os subgrupos e comandos disponíveis na [**pesquisa az**](/cli/azure/search) a partir do CLI. Em alternativa, pode rever a [documentação.](/cli/azure/search)

Para visualizar os subgrupos disponíveis `az search` dentro, executar o seguinte comando.

```azurecli-interactive
az search --help
```

A resposta deve ser semelhante à seguinte.

```
Group
    az search : Manage Azure Search services, admin keys and query keys.
        WARNING: This command group is in preview and under development. Reference and support
        levels: https://aka.ms/CLI_refstatus
Subgroups:
    admin-key                    : Manage Azure Search admin keys.
    private-endpoint-connection  : Manage Azure Search private endpoint connections.
    private-link-resource        : Manage Azure Search private link resources.
    query-key                    : Manage Azure Search query keys.
    service                      : Manage Azure Search services.
    shared-private-link-resource : Manage Azure Search shared private link resources.

For more specific examples, use: az find "az search"
```

Dentro de cada subgrupo, estão disponíveis vários comandos. Pode ver os comandos disponíveis para o `service` subgrupo executando a seguinte linha.

```azurecli-interactive
az search service --help
```

Também pode ver os argumentos disponíveis para um determinado comando.

```azurecli-interactive
az search service create --help
```

## <a name="get-search-service-information"></a>Obtenha informações do serviço de pesquisa

Se conhecer o grupo de recursos que contém o seu serviço de pesquisa, executar [**o serviço de pesquisa az mostrar**](/cli/azure/search/service#az_search_service_show) para devolver a definição de serviço, incluindo nome, região, nível e replicação e contagem de divisórias.

```azurecli-interactive
az search service show --name <service-name> --resource-group <resource-group-name>
```

## <a name="create-or-delete-a-service"></a>Criar ou apagar um serviço

Para [criar um novo serviço de pesquisa,](search-create-service-portal)utilize o serviço de pesquisa [**az criar**](/cli/azure/search/service#az_search_service_show) comando.

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1
``` 

Os resultados devem ser semelhantes aos seguintes resultados:

```
{
  "hostingMode": "default",
  "id": "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp",
  "identity": null,
  "location": "West US",
  "name": "my-demo-searchapp",
  "networkRuleSet": {
    "bypass": "None",
    "ipRules": []
  },
  "partitionCount": 1,
  "privateEndpointConnections": [],
  "provisioningState": "succeeded",
  "publicNetworkAccess": "Enabled",
  "replicaCount": 1,
  "resourceGroup": "demo-westus",
  "sharedPrivateLinkResources": [],
  "sku": {
    "name": "standard"
  },
  "status": "running",
  "statusDetails": "",
  "tags": null,
  "type": "Microsoft.Search/searchServices"
}
```

### <a name="create-a-service-with-ip-rules"></a>Criar um serviço com regras IP

Dependendo dos seus requisitos de segurança, poderá querer criar um serviço de pesquisa com uma [firewall IP configurada](service-configure-firewall.md). Para tal, passe os endereços IP (v4) públicos ou os intervalos CIDR para o `ip-rules` argumento como mostrado abaixo. As regras devem ser separadas por uma vírgula `,` ( ) ou emicolon ( `;` ).

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --ip-rules "55.5.63.73;52.228.215.197;101.37.221.205"
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>Criar um serviço com um sistema atribuído identidade gerida

Em alguns casos, como quando [se utiliza identidade gerida para ligar a uma fonte de dados,](search-howto-managed-identities-storage.md)terá de ligar a [identidade gerida do sistema atribuída.](../active-directory/managed-identities-azure-resources/overview.md) Isto é feito adicionando `--identity-type SystemAssigned` ao comando.

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --identity-type SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>Criar um serviço com um ponto final privado

[Os pontos finais privados](../private-link/private-endpoint-overview.md) para a Azure Cognitive Search permitem que um cliente numa rede virtual aceda de forma segura a dados num índice de pesquisa sobre um [Link Privado](../private-link/private-link-overview.md). O ponto final privado utiliza um endereço IP a partir do [espaço de endereço de rede virtual](../virtual-network/private-ip-addresses.md) para o seu serviço de pesquisa. O tráfego de rede entre o cliente e o serviço de pesquisa atravessa a rede virtual e uma ligação privada na rede de espinha dorsal da Microsoft, eliminando a exposição da internet pública. Para mais detalhes, consulte a documentação sobre a [criação de um ponto final privado para a Pesquisa Cognitiva do Azure](service-create-private-endpoint.md)

O exemplo a seguir mostra como criar um serviço de pesquisa com um ponto final privado.

Em primeiro lugar, implementar um serviço de pesquisa com `PublicNetworkAccess` set to `Disabled` .

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --public-access Disabled
```

Em seguida, crie uma rede virtual e o ponto final privado.

```azurecli-interactive
# Create the virtual network
az network vnet create \
    --resource-group <resource-group-name> \
    --location "West US" \
    --name <virtual-network-name> \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name <subnet-name> \
    --subnet-prefixes 10.1.0.0/24

# Update the subnet to disable private endpoint network policies
az network vnet subnet update \
    --name <subnet-name> \
    --resource-group <resource-group-name> \
    --vnet-name <virtual-network-name> \
    --disable-private-endpoint-network-policies true

# Get the id of the search service
id=$(az search service show \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --query [id]' \
    --output tsv)

# Create the private endpoint
az network private-endpoint create \
    --name <private-endpoint-name> \
    --resource-group <resource-group-name> \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --private-connection-resource-id $id \
    --group-id searchService \
    --connection-name <private-link-connection-name>  
```

Finalmente, crie uma Zona DE DNS privada. 

```azurecli-interactive
## Create private DNS zone
az network private-dns zone create \
    --resource-group <resource-group-name> \
    --name "privatelink.search.windows.net"

## Create DNS network link
az network private-dns link vnet create \
    --resource-group <resource-group-name> \
    --zone-name "privatelink.search.windows.net" \
    --name "myLink" \
    --virtual-network <virtual-network-name> \
    --registration-enabled false

## Create DNS zone group
az network private-endpoint dns-zone-group create \
   --resource-group <resource-group-name>\
   --endpoint-name <private-endpoint-name> \
   --name "myZoneGroup" \
   --private-dns-zone "privatelink.search.windows.net" \
   --zone-name "searchServiceZone"
```

Para obter mais informações sobre a criação de pontos finais privados no PowerShell, consulte este [Quickstart de Ligação Privada](https://docs.microsoft.com/azure/private-link/create-private-endpoint-cli)

### <a name="manage-private-endpoint-connections"></a>Gerir ligações privadas de ponto final

Além de criar uma ligação de ponto final privado, também `show` `update` pode, e `delete` a ligação.

Para obter uma ligação de ponto final privado e para ver o seu estado, utilize [**um show de ligação de pontos de extremidade privada**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_show).

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Para atualizar a ligação, utilize [**a az search private-endpoint-connection update**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_update). O exemplo a seguir define uma ligação privada de ponto final para rejeitado:

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
    --status Rejected \
    --description "Rejected" \
    --actions-required "Please fix XYZ"
```

Para eliminar a ligação de ponto final privado, utilize [**a az procure excluir de ligação**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_delete)de pontos de extremidade privada .

```azurecli-interactive
az search private-endpoint-connection delete \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

## <a name="regenerate-admin-keys"></a>Regenerar chaves de administração

Para rebolar [as teclas API de administração,](search-security-api-keys.md)utilize [**a renovação da chave de administração de pesquisa az**](/cli/azure/search/admin-key#az_search_admin_key_renew). São criadas duas teclas de administração com cada serviço para acesso autenticado. As chaves são necessárias em todos os pedidos. Ambas as teclas de administração são funcionalmente equivalentes, concedendo acesso total a um serviço de pesquisa com a capacidade de recuperar qualquer informação, ou criar e eliminar qualquer objeto. Existem duas chaves para que possa utilizar uma durante a substituição da outra. 

Só se pode regenerar um de cada vez, especificado como a `primary` chave ou `secondary` a chave. Para um serviço ininterrupto, lembre-se de atualizar todo o código do cliente para utilizar uma chave secundária enquanto roda a chave primária. Evite trocar as chaves durante o voo.

Como seria de esperar, se regenerar chaves sem atualizar o código do cliente, os pedidos que utilizam a tecla antiga falharão. A regeneração de todas as chaves novas não o bloqueia permanentemente do seu serviço, podendo ainda aceder ao serviço através do portal. Depois de regenerar as teclas primárias e secundárias, pode atualizar o código do cliente para utilizar as novas teclas e as operações serão retomadas em conformidade.

Os valores das teclas API são gerados pelo serviço. Não é possível fornecer uma chave personalizada para a Azure Cognitive Search usar. Da mesma forma, não existe um nome definido pelo utilizador para as teclas API administradas. As referências à chave são cordas fixas, ou `primary` `secondary` . 

```azurecli-interactive
az search admin-key renew \
    --resource-group <resource-group-name> \
    --service-name <search-service-name> \
    --key-kind primary
```

Os resultados devem ser semelhantes à seguinte saída. Ambas as chaves são devolvidas, mesmo que só mude uma de cada vez.

```   
{
  "primaryKey": <alphanumeric-guid>,
  "secondaryKey": <alphanumeric-guid>  
}
```

## <a name="create-or-delete-query-keys"></a>Criar ou eliminar chaves de consulta

Para criar [teclas API](search-security-api-keys.md) de consulta para acesso apenas de leitura de aplicações de clientes a um índice de pesquisa cognitiva Azure, utilize [**a az pesquisa-key create**](/cli/azure/search/query-key#az_search_query_key_create). As teclas de consulta são usadas para autenticar um índice específico com o objetivo de recuperar os resultados da pesquisa. As teclas de consulta não concedem acesso apenas de leitura a outros itens do serviço, tais como um índice, fonte de dados ou indexante.

Não é possível fornecer uma chave para a Pesquisa Cognitiva Azure usar. As chaves API são geradas pelo serviço.

```azurecli-interactive
az search query-key create \
    --name myQueryKey \
    --resource-group <resource-group-name> \
    --service-name <search-service-name>
```

## <a name="scale-replicas-and-partitions"></a>Réplicas e divisórias em escala

Para [aumentar ou diminuir réplicas e divisórias](search-capacity-planning.md) utilize a [**atualização do serviço de pesquisa az**](/cli/azure/search/service#az_search_service_update). O aumento de réplicas ou divisórias adiciona à sua conta, que tem encargos fixos e variáveis. Se tiver uma necessidade temporária de poder de processamento adicional, pode aumentar réplicas e divisórias para lidar com a carga de trabalho. A área de monitorização na página do portal Overview tem azulejos sobre latência de consulta, consultas por segundo e estrangulamento, indicando se a capacidade atual é adequada.

Pode demorar um pouco para adicionar ou remover o reabastecimento. Os ajustes à capacidade ocorrem em segundo plano, permitindo que as cargas de trabalho existentes continuem. A capacidade adicional é utilizada para pedidos de entrada assim que estiver pronto, sem necessidade de configuração adicional. 

Remover a capacidade pode ser perturbador. Recomenda-se que se impeçam todos os postos de trabalho de indexação e indexante antes de reduzir a capacidade para evitar pedidos de abandono. Se isso não for viável, pode considerar reduzir a capacidade incrementalmente, uma réplica e partição de cada vez, até que os seus novos níveis de alvo sejam atingidos.

Uma vez que envie o comando, não há como terminá-lo a meio do caminho. Terá de esperar até que o comando termine antes de rever as contagens.

```azurecli-interactive
az search service update \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --partition-count 6 \
    --replica-count 6
```

Além da atualização das contagens de réplicas e partições, também pode atualizar `ip-rules` `public-access` , e `identity-type` .

## <a name="create-a-shared-private-link-resource"></a>Criar um recurso de ligação privada partilhada

Os pontos finais privados de recursos seguros que são criados através das APIs de Pesquisa Cognitiva Azure são referidos como *recursos de ligação privada partilhados.* Isto porque está a "partilhar" o acesso a um recurso, como uma conta de armazenamento, que foi integrado com o [serviço Azure Private Link.](https://azure.microsoft.com/services/private-link/)

Se estiver a utilizar um indexante para indexar dados na Pesquisa Cognitiva Azure, e a sua fonte de dados estiver numa rede privada, pode criar uma [ligação de ponto final privado](../private-link/private-endpoint-overview.md) de saída para chegar aos dados.

Uma lista completa dos Recursos Azure para os quais pode criar pontos finais privados de saída da Azure Cognitive Search pode ser encontrada [aqui](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis) juntamente com os valores de **ID do Grupo** relacionados.

Para criar o recurso de ligação privada partilhada, utilize [**a az search shared-private-link-resource create**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list). Tenha em mente que poderá ser necessária alguma configuração para a fonte de dados antes de executar este comando.

```azurecli-interactive
az search shared-private-link-resource create \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> \
    --group-id blob \
    --resource-id "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage"  \
    --request-message "Please approve" 
```


Para recuperar os recursos de ligação privada partilhada e ver o seu estado, utilize [**a lista de recursos de ligação partilhada e privada.**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list)

```azurecli-interactive
az search shared-private-link-resource list \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Terá de aprovar a ligação com o seguinte comando antes de poder ser utilizado. A identificação da ligação do ponto final privado terá de ser recuperada a partir do recurso da criança. Neste caso, obtemos a identificação de ligação do armazenamento az.

```azurecli-interactive
id = (az storage account show -n myBlobStorage --query "privateEndpointConnections[0].id")

az network private-endpoint-connection approve --id $id
```

Para eliminar o recurso de link privado partilhado, utilize [**a az search shared-private-link-resource delete**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_delete).

```azurecli-interactive
az search shared-private-link-resource delete \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Para obter todos os detalhes sobre a criação de recursos de ligação privada partilhados, consulte a documentação sobre [a realização de ligações indexantes através de um ponto final privado](search-indexer-howto-access-private.md).

## <a name="next-steps"></a>Passos seguintes

Construa um [índice,](search-what-is-an-index.md) [consultar um índice](search-query-overview.md) utilizando o portal, AS APIs de REST ou o .NET SDK.

* [Criar um índice de Pesquisa Cognitiva Azure no portal Azure](search-get-started-portal.md)
* [Configurar um indexador para carregar dados de outros serviços](search-indexer-overview.md)
* [Consulta um índice de Pesquisa Cognitiva Azure usando explorador de pesquisa no portal Azure](search-explorer.md)
* [Como utilizar a Azure Cognitive Search em .NET](search-howto-dotnet-sdk.md)
