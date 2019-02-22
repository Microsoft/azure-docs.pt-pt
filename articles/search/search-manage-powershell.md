---
title: Gerir um serviço de Azure Search com scripts do Powershell - Azure Search
description: Gerir o seu serviço de Azure Search com scripts do PowerShell. Criar ou atualizar um serviço Azure Search e gerir chaves de administração do Azure Search
author: HeidiSteen
manager: cgronlun
tags: azure-resource-manager
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/15/2016
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 86f8eebb8e174b4a4d4dbdc9def516e23b79a131
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56591657"
---
# <a name="manage-your-azure-search-service-with-powershell"></a>Gerir o seu serviço de Azure Search com o PowerShell
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> 
> 

Este tópico descreve os comandos do PowerShell para efetuar muitas das tarefas de gestão de serviços de pesquisa do Azure. Percorreremos a criação de um serviço de pesquisa, o dimensionamento-lo e gerir as chaves de API.
Estes comandos as opções de gestão disponíveis em paralelo os [API do REST de gestão do Azure Search](https://docs.microsoft.com/rest/api/searchmanagement).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Tem de ter o Azure PowerShell. Para obter instruções de instalação, consulte [instalar o Azure PowerShell](/powershell/azure/overview).
* Tem de ter sessão iniciada sua subscrição do Azure no PowerShell, conforme descrito abaixo.

Em primeiro lugar, tem de iniciar sessão no Azure com este comando:

    Connect-AzAccount

Especifica o endereço de e-mail da sua conta do Azure e a respetiva palavra-passe na caixa de diálogo de início de sessão da Microsoft Azure.

Em alternativa, pode [início de sessão forma não interativa com um principal de serviço](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

Se tiver várias subscrições do Azure, terá de definir a sua subscrição do Azure. Para ver uma lista das suas subscrições atuais, execute este comando.

    Get-AzSubscription | sort SubscriptionName | Select SubscriptionName

Para especificar a subscrição, execute o seguinte comando. No exemplo a seguir, é o nome da subscrição `ContosoSubscription`.

    Select-AzSubscription -SubscriptionName ContosoSubscription

## <a name="commands-to-help-you-get-started"></a>Comandos para ajudá-lo a começar a utilizar
    $serviceName = "your-service-name-lowercase-with-dashes"
    $sku = "free" # or "basic" or "standard" for paid services
    $location = "West US"
    # You can get a list of potential locations with
    # (Get-AzResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
    $resourceGroupName = "YourResourceGroup" 
    # If you don't already have this resource group, you can create it with 
    # New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Register the ARM provider idempotently. This must be done once per subscription
    Register-AzResourceProvider -ProviderNamespace "Microsoft.Search"

    # Create a new search service
    # This command will return once the service is fully created
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateUri "https://gallery.azure.com/artifact/20151001/Microsoft.Search.1.0.9/DeploymentTemplates/searchServiceDefaultTemplate.json" `
        -NameFromTemplate $serviceName `
        -Sku $sku `
        -Location $location `
        -PartitionCount 1 `
        -ReplicaCount 1

    # Get information about your new service and store it in $resource
    $resource = Get-AzResource `
        -ResourceType "Microsoft.Search/searchServices" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19

    # View your resource
    $resource

    # Get the primary admin API key
    $primaryKey = (Invoke-AzResourceAction `
        -Action listAdminKeys `
        -ResourceId $resource.ResourceId `
        -ApiVersion 2015-08-19).PrimaryKey

    # Regenerate the secondary admin API Key
    $secondaryKey = (Invoke-AzResourceAction `
        -ResourceType "Microsoft.Search/searchServices/regenerateAdminKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action secondary).SecondaryKey

    # Create a query key for read only access to your indexes
    $queryKeyDescription = "query-key-created-from-powershell"
    $queryKey = (Invoke-AzResourceAction `
        -ResourceType "Microsoft.Search/searchServices/createQueryKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action $queryKeyDescription).Key

    # View your query key
    $queryKey

    # Delete query key
    Remove-AzResource `
        -ResourceType "Microsoft.Search/searchServices/deleteQueryKey/$($queryKey)" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19

    # Scale your service up
    # Note that this will only work if you made a non "free" service
    # This command will not return until the operation is finished
    # It can take 15 minutes or more to provision the additional resources
    $resource.Properties.ReplicaCount = 2
    $resource | Set-AzResource

    # Delete your service
    # Deleting your service will delete all indexes and data in the service
    $resource | Remove-AzResource

## <a name="next-steps"></a>Próximos Passos
Agora que o serviço é criado, pode efetuar os passos seguintes: criar uma [índice](search-what-is-an-index.md), [consultar um índice](search-query-overview.md)e, finalmente, criar e gerir o seu próprio aplicativo de pesquisa que utiliza a Azure Search.

* [Criar um índice da Azure Search no portal do Azure](search-create-index-portal.md)
* [Consultar um índice da Azure Search utilizando o Explorador de pesquisa no portal do Azure](search-explorer.md)
* [Configurar um indexador para carregar dados de outros serviços](search-indexer-overview.md)
* [Como utilizar o Azure Search no .NET](search-howto-dotnet-sdk.md)
* [Analisar o tráfego do Azure Search](search-traffic-analytics.md)

