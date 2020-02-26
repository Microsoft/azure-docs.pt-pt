---
title: Como mover o seu recurso de serviço através de regiões
titleSuffix: Azure Cognitive Search
description: Este artigo irá mostrar-lhe como mover os seus recursos de Pesquisa Cognitiva Azure de uma região para outra na nuvem Azure.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/18/2020
ms.openlocfilehash: 392c86d8ea24e59d388926d4df581305ea2b531d
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599304"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Mova o seu serviço de Pesquisa Cognitiva Azure para outra região de Azure

Para mover a sua conta de Serviço Cognitivo Azure de uma região para outra, irá criar um modelo de exportação para mover a sua subscrição( s). Depois de mover a sua subscrição, terá de mover os seus dados e recriar o seu serviço.

Neste artigo, aprenderá a:

> [!div class="checklist"]
> * Exportar um modelo.
> * Modificar o modelo: adicionar a região alvo, nomes de conta de pesquisa e armazenamento.
> * Implemente o modelo para criar as novas contas de pesquisa e armazenamento.
> * Verifique o seu estado de serviço na nova região
> * Limpe os recursos na região de origem.

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que os serviços e funcionalidades que a sua conta utiliza são suportados na região alvo.

- Para funcionalidades de pré-visualização, certifique-se de que a sua subscrição está listada para a região alvo. Para obter mais informações sobre funcionalidades de pré-visualização, consulte lojas de [conhecimento,](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro) [enriquecimento incremental](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual)e ponto [final privado.](https://docs.microsoft.com/azure/search/service-create-private-endpoint)

## <a name="assessment-and-planning"></a>Avaliação e planeamento

Quando mudar o seu serviço de pesquisa para a nova região, terá de [transferir os seus dados para o novo serviço](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account) de armazenamento e, em seguida, reconstruir os seus índices, habilidades e lojas de conhecimento. Deve gravar as definições atuais e copiar ficheiros json para tornar a reconstrução do seu serviço mais fácil e rápida.

## <a name="moving-your-search-services-resources"></a>Mover os recursos do seu serviço de pesquisa

Para começar, exportará e modificará um modelo de Gestor de Recursos.

### <a name="export-a-template"></a>Exportar um modelo

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Vá à sua página do Grupo de Recursos.

> [!div class="mx-imgBorder"]
> ![Grupo de Recursos exemplo](./media/search-move-resource/export-template-sample.png)

3. Selecione **Todos os recursos**.

3. No menu de navegação à esquerda selecione **Modelo de exportação**.

4. Escolha **o Download** na página do modelo de **exportação.**

5. Localize o ficheiro .zip que descarregou do portal e desaperte esse ficheiro para uma pasta à sua escolha.

O ficheiro zip contém os ficheiros .json que compõem o modelo e os scripts para implementar o modelo.

### <a name="modify-the-template"></a>Modificar o modelo

Irá modificar o modelo alterando os nomes e regiões da conta de pesquisa e armazenamento. Os nomes devem seguir as regras para cada serviço e convenções de nomeação da região. 

Para obter códigos de localização da região, consulte [localizações azure](https://azure.microsoft.com/global-infrastructure/locations/).  O código para uma região é o nome da região sem espaços, **Centro dos EUA** = **central.**

1. No portal do Azure, selecione **Criar um recurso**.

2. Em **Pesquisar no Marketplace**, escreva **implementação de modelo** e prima **ENTER**.

3. Selecione **Implementação de modelo**.

4. Selecione **Criar**.

5. Selecione **Crie o seu próprio modelo no editor**.

6. Selecione **ficheiro Load**, e siga as instruções para carregar o ficheiro **template.json** que descarregou e desfechou na secção anterior.

7. No ficheiro **template.json,** nomeie as contas de pesquisa e armazenamento do alvo, definindo o valor padrão dos nomes da conta de pesquisa e armazenamento. 

8. Editar a propriedade de **localização** no ficheiro **template.json** para a região alvo tanto para os seus serviços de pesquisa como armazenamento. Este exemplo define a região-alvo para `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2015-08-19",
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

### <a name="deploy-the-template"></a>Implementar o modelo

1. Guarde o ficheiro **template.json.**

2. Insira ou selecione os valores de propriedade:

- **Subscrição**: selecione uma subscrição do Azure.

- **Grupo de recursos**: selecione **Criar novo** e dê um nome ao grupo de recursos.

- **Localização**: Selecione uma localização Azure.

3. Clique no **I concordando com os termos e condições indicados acima da** caixa de verificação e, em seguida, clique no botão **'Selecionar Comprar'.**

## <a name="verifying-your-services-status-in-new-region"></a>Verificar o estado dos seus serviços na nova região

Para verificar a mudança, abra o novo grupo de recursos e os seus serviços serão listados na nova região.

Para mover os seus dados da sua região de origem para a região alvo, consulte as diretrizes deste artigo para [mover os seus dados para a nova conta](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account)de armazenamento .

## <a name="clean-up-resources-in-your-original-region"></a>Limpe os recursos na sua região original

Para efazer as alterações e concluir o movimento da sua conta de serviço, elimine a conta de serviço de origem.

## <a name="next-steps"></a>Passos seguintes

[Criar um índice](https://docs.microsoft.com/azure/search/search-get-started-portal)

[Criar uma habilidade](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[Criar uma loja de conhecimento](https://docs.microsoft.com/azure/search/knowledge-store-create-portal)

