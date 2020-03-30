---
title: Como mover o seu recurso de serviço através de regiões
titleSuffix: Azure Cognitive Search
description: Este artigo irá mostrar-lhe como mover os seus recursos de Pesquisa Cognitiva Azure de uma região para outra na nuvem Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 03/24/2020
ms.openlocfilehash: 00f16d11f7a9cd276772eda5e91d6e117ada8c9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246308"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Mova o seu serviço de Pesquisa Cognitiva Azure para outra região de Azure

Ocasionalmente, os clientes perguntam sobre a mudança de um serviço de pesquisa para outra região. Atualmente, não existe nenhum mecanismo incorporado ou ferramenta para ajudar nessa tarefa, mas este artigo pode ajudá-lo a entender os passos manuais para alcançar o mesmo resultado.

> [!NOTE]
> No portal Azure, todos os serviços têm um comando **de modelo de exportação.** No caso da Pesquisa Cognitiva Azure, este comando produz uma definição básica de um serviço (nome, localização, nível, réplica e contagem de partição), mas não reconhece o conteúdo do seu serviço, nem transporta chaves, funções ou registos. Embora o comando exista, não recomendamos usá-lo para mover um serviço de pesquisa.

## <a name="guidance-for-moving-a-service"></a>Orientação para mover um serviço

1. Identifique dependências e serviços relacionados para entender o impacto total da deslocalização de um serviço, caso precise mover-se mais do que apenas a Pesquisa Cognitiva Azure.

   O Armazenamento Azure é usado para a exploração madeireira, criando uma loja de conhecimento, e é uma fonte de dados externa sumidamente utilizada para enriquecimento e indexação de IA. Os Serviços Cognitivos são uma dependência do enriquecimento de IA. Tanto os Serviços Cognitivos como o seu serviço de pesquisa são obrigados a estar na mesma região se estiver a usar o enriquecimento de IA.

1. Crie um inventário de todos os objetos no serviço para que saiba o que mover: índices, mapas de sinónimo, indexadores, fontes de dados, skillsets. Se habilitasse a exploração madeireira, criasse e archiveasse quaisquer relatórios que precisasse para um registo histórico.

1. Consulte os preços e disponibilidade na nova região para garantir a disponibilidade de Pesquisa Cognitiva Azure mais quaisquer serviços relacionados na nova região. A maioria das funcionalidades estão disponíveis em todas as regiões, mas algumas funcionalidades de pré-visualização têm disponibilidade restrita.

1. Crie um serviço na nova região e republique a partir do código fonte quaisquer índices existentes, mapas de sinónimo, indexadores, fontes de dados e skillsets. Lembre-se que os nomes de serviço devem ser únicos para que não possa reutilizar o nome existente. Verifique cada skillset para ver se as ligações aos Serviços Cognitivos ainda são válidas em termos do requisito da mesma região. Além disso, se forem criadas lojas de conhecimento, verifique as cordas de ligação para o Armazenamento Azure se estiver a utilizar um serviço diferente.

1. Recarregar índices e lojas de conhecimento, se aplicável. Ou usará o código de aplicação para empurrar os dados da JSON para um índice, ou reexecutar os indexadores para obter documentos de fontes externas. 

1. Ative a exploração madeireira e, se estiver a usá-las, recrie funções de segurança.

1. Atualize as aplicações do cliente e as suítes de teste para usar o novo nome de serviço e chaves API, e teste todas as aplicações.

1. Elimine o serviço antigo assim que o novo serviço estiver totalmente testado e operacional.

## <a name="next-steps"></a>Passos seguintes

Os seguintes links podem ajudá-lo a localizar mais informações ao completar os passos acima descritos.

+ [Preços e regiões de pesquisa cognitiva azure](https://azure.microsoft.com/pricing/details/search/)
+ [Escolha um escalão](search-sku-tier.md)
+ [Criar um serviço de pesquisa](search-create-service-portal.md)
+ [Carregar documentos de pesquisa](search-what-is-data-import.md)
+ [Ativar registo](search-monitor-logs.md)


<!-- To move your Azure Cognitive Service account from one region to another, you will create an export template to move your subscription(s). After moving your subscription, you will need to move your data and recreate your service.

In this article, you'll learn how to:

> [!div class="checklist"]
> * Export a template.
> * Modify the template: adding the target region, search and storage account names.
> * Deploy the template to create the new search and storage accounts.
> * Verify your service status in the new region
> * Clean up resources in the source region.

## Prerequisites

- Ensure that the services and features that your account uses are supported in the target region.

- For preview features, ensure that your subscription is whitelisted for the target region. For more information about preview features, see [knowledge stores](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro), [incremental enrichment](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual), and [private endpoint](https://docs.microsoft.com/azure/search/service-create-private-endpoint).

## Assessment and planning

When you move your search service to the new region, you will need to [move your data to the new storage service](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account) and then rebuild your indexes, skillsets and knowledge stores. You should record current settings and copy json files to make the rebuilding of your service easier and faster.

## Moving your search service's resources

To start you will export and then modify a Resource Manager template.

### Export a template

1. Sign in to the [Azure portal](https://portal.azure.com).

2. Go to your Resource Group page.

> [!div class="mx-imgBorder"]
> ![Resource Group page example](./media/search-move-resource/export-template-sample.png)

3. Select **All resources**.

3. In the left hand navigation menu select **Export template**.

4. Choose **Download** in the **Export template** page.

5. Locate the .zip file that you downloaded from the portal, and unzip that file to a folder of your choice.

The zip file contains the .json files that comprise the template and scripts to deploy the template.

### Modify the template

You will modify the template by changing the search and storage account names and regions. The names must follow the rules for each service and region naming conventions. 

To obtain region location codes, see [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  The code for a region is the region name with no spaces, **Central US** = **centralus**.

1. In the Azure portal, select **Create a resource**.

2. In **Search the Marketplace**, type **template deployment**, and then press **ENTER**.

3. Select **Template deployment**.

4. Select **Create**.

5. Select **Build your own template in the editor**.

6. Select **Load file**, and then follow the instructions to load the **template.json** file that you downloaded and unzipped in the previous section.

7. In the **template.json** file, name the target search and storage accounts by setting the default value of the search and storage account names. 

8. Edit the **location** property in the **template.json** file to the target region for both your search and storage services. This example sets the target region to `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2020-03-13",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### Deploy the template

1. Save the **template.json** file.

2. Enter or select the property values:

- **Subscription**: Select an Azure subscription.

- **Resource group**: Select **Create new** and give the resource group a name.

- **Location**: Select an Azure location.

3. Click the **I agree to the terms and conditions stated above** checkbox, and then click the **Select Purchase** button.

## Verifying your services' status in new region

To verify the move, open the new resource group and your services will be listed with the new region.

To move your data from your source region to the target region, please see this article's guidelines for [moving your data to the new storage account](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account).

## Clean up resources in your original region

To commit the changes and complete the move of your service account, delete the source service account.

## Next steps

[Create an index](https://docs.microsoft.com/azure/search/search-get-started-portal)

[Create a skillset](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[Create a knowledge store](https://docs.microsoft.com/azure/search/knowledge-store-create-portal) -->