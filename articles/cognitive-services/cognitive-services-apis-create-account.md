---
title: Criar um recurso de serviços cognitivas no portal do Azure
titlesuffix: Azure Cognitive Services
description: Introdução aos serviços cognitivas do Azure criando e assinando um recurso no portal do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: aahi
ms.openlocfilehash: e9616f3014288e4b2580f474c49c646928db5a08
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334242"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Criar um recurso de serviços cognitivas usando o portal do Azure

Use este guia de início rápido para começar a usar os serviços cognitivas do Azure usando o portal do Azure. Os serviços cognitivas são representados pelos [recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) do Azure que você cria em sua assinatura do Azure. Depois de criar o recurso, use as chaves e o ponto de extremidade gerados para autenticar seus aplicativos. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura válida do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>Criar um novo recurso de serviços cognitivas do Azure

Antes de criar um recurso de serviços cognitivas, você deve ter um grupo de recursos do Azure para conter o recurso. Ao criar um novo recurso, você tem a opção de criar um novo grupo de recursos ou usar um existente. Este artigo mostra como criar um novo grupo de recursos.

1. Entre no [portal do Azure](https://portal.azure.com)e clique em **+ criar um recurso**.

    ![Selecionar API de Serviços Cognitivos](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Você pode encontrar serviços cognitivas disponíveis com as seguintes maneiras:
    * Use a barra de pesquisa e insira o nome do serviço que você deseja assinar.
        * Para criar um recurso de vários serviços, insira **Serviços cognitivas** na barra de pesquisa e selecione o recurso **Serviços cognitivas** .

        ![Pesquisar serviços cognitivas](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    * Para ver todos os serviços cognitivas disponíveis, selecione **ia + Machine Learning**, no **Azure Marketplace**. Se você não vir o serviço no qual está interessado, clique em **Ver todos** e role até **Serviços cognitivas**. Clique em **mais** para exibir todo o catálogo de API de serviços cognitivos.
    
        ![Selecionar API de Serviços Cognitivos](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. Na página **criar** , forneça as seguintes informações:

    > [!IMPORTANT]
    > Lembre-se do local do Azure, pois você pode precisar dele ao chamar os serviços cognitivas do Azure.

    |    |    |
    |--|--|
    | **Name** | Um nome descritivo para seu recurso de serviços cognitivas. Por exemplo, *MyCognitiveServicesAccount*. |
    | **Subscrição** | Selecione uma das suas assinaturas do Azure disponíveis. |
    | **Location** | O local de sua instância de serviço cognitiva. Locais diferentes podem introduzir latência, mas não têm impacto sobre a disponibilidade de tempo de execução do recurso. |
    | **Escalão de preço** | O custo de sua conta de serviços cognitivas depende das opções que você escolher e do uso. Para obter mais informações, consulte os [detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/)da API.
    | **Grupo de recursos** | O [grupo de recursos do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) que conterá seu recurso de serviços cognitivas. Você pode criar um novo grupo ou adicioná-lo a um grupo já existente. |

    ![Tela de criação de recursos](media/cognitive-services-apis-create-account/resource_create_screen.png)


## <a name="get-the-keys-for-your-resource"></a>Obter as chaves para seu recurso

Depois de criar o recurso, você poderá acessá-lo no painel do Azure se o tiver fixado. Caso contrário, você pode encontrá-lo em **grupos de recursos**. Depois de selecionar o recurso, você pode obter as chaves selecionando **chaves** em **Gerenciamento de recursos**.

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos contidos no grupo.

Para remover um grupo de recursos através do portal do Azure:

1. No portal do Azure, expanda o menu no lado esquerdo para abrir o menu de serviços e escolha **Grupos de Recursos**, para apresentar a lista dos seus grupos de recursos.
2. Localize o grupo de recursos a ser excluído e clique com o botão direito do mouse no botão mais (...) no lado direito da listagem.
3. Selecione **Eliminar grupo de recursos** e confirme.

## <a name="see-also"></a>Consulte também

* [Autenticar solicitações para serviços cognitivas do Azure](authentication.md)
* [O que são os serviços cognitivas do Azure?](Welcome.md)
* [Suporte a idioma natural](language-support.md)
* [Suporte a contêiner do Docker](cognitive-services-container-support.md)
