---
title: Criar um recurso de serviços cognitivas no portal do Azure
titleSuffix: Azure Cognitive Services
description: Introdução aos serviços cognitivas do Azure criando e assinando um recurso no portal do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: aahi
ms.openlocfilehash: c7db2b4d49e3b9297c32d2e11ffe7c7702c17544
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274584"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Criar um recurso de serviços cognitivas usando o portal do Azure

Use este guia de início rápido para criar um recurso dos serviços cognitivas do Azure usando o portal do Azure. Depois de criar com êxito um recurso de serviços cognitivas, você obterá um ponto de extremidade e uma chave que você pode usar para autenticar seus aplicativos.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura válida do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/).

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>Criar um novo recurso de serviços cognitivas do Azure

Antes de criar um recurso de serviços cognitivas, você deve ter um grupo de recursos do Azure para conter o recurso. Ao criar um novo recurso, você tem a opção de criar um novo grupo de recursos ou usar um existente. Este artigo mostra como criar um novo grupo de recursos.

1. Entre no [portal do Azure](https://portal.azure.com)e clique em **+ criar um recurso**.

    ![Selecionar API de Serviços Cognitivos](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Conforme explicado anteriormente, você pode criar um recurso de serviços cognitivas de duas maneiras: usando um recurso de vários serviços ou um recurso de serviço único.

    #### <a name="multi-service-resourcetabmultiservice"></a>[Recurso de vários serviços](#tab/multiservice)

    Para criar um recurso de vários serviços, insira **Serviços cognitivas** na barra de pesquisa.

    ![Pesquisar serviços cognitivas](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    Na página serviços cognitivas, selecione **criar**.

    ![Criar conta de serviços cognitivas](media/cognitive-services-apis-create-account/azurecogservsearchmulti-2.png)

    #### <a name="single-service-resourcetabsingleservice"></a>[Recurso de serviço único](#tab/singleservice)

    Para ver todos os serviços cognitivas disponíveis, selecione **ia + Machine Learning**, no **Azure Marketplace**. Se você não vir o serviço no qual está interessado, clique em **Ver todos** e role até **Serviços cognitivas**. Clique em **Ver mais** para exibir todo o catálogo de serviços cognitivas.

    Quando você estiver no serviço no qual está interessado, clique em **criar**.
    
    ![Selecionar API de Serviços Cognitivos](media/cognitive-services-apis-create-account/azureMarketplace.png)

    ***
3. Na página **criar** , forneça as seguintes informações:

    #### <a name="multi-service-resourcetabmultiservice"></a>[Recurso de vários serviços](#tab/multiservice)

    |    |    |
    |--|--|
    | **Name** | Um nome descritivo para seu recurso de serviços cognitivas. Por exemplo, *MyCognitiveServicesResource*. |
    | **Subscrição** | Selecione uma das suas assinaturas do Azure disponíveis. |
    | **Location** | O local de sua instância de serviço cognitiva. Locais diferentes podem introduzir latência, mas não têm impacto sobre a disponibilidade de tempo de execução do recurso. Lembre-se do local do Azure, pois você pode precisar dele ao chamar os serviços cognitivas do Azure. |
    | **Escalão de preço** | O custo de sua conta de serviços cognitivas depende das opções que você escolher e do uso. Para obter mais informações, consulte os [detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/)da API.
    | **Grupo de recursos** | O grupo de recursos do Azure que conterá seu recurso de serviços cognitivas. Você pode criar um novo grupo ou adicioná-lo a um grupo já existente. |

    ![Tela de criação de recursos](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Clique em **Criar**.

    #### <a name="single-service-resourcetabsingleservice"></a>[Recurso de serviço único](#tab/singleservice)

    |    |    |
    |--|--|
    | **Name** | Um nome descritivo para seu recurso de serviços cognitivas. Por exemplo, *TextAnalyticsResource*. |
    | **Subscrição** | Selecione uma das suas assinaturas do Azure disponíveis. |
    | **Location** | O local de sua instância de serviço cognitiva. Locais diferentes podem introduzir latência, mas não têm impacto sobre a disponibilidade de tempo de execução do recurso. Lembre-se do local do Azure, pois você pode precisar dele ao chamar os serviços cognitivas do Azure. |
    | **Escalão de preço** | O custo de sua conta de serviços cognitivas depende das opções que você escolher e do uso. Para obter mais informações, consulte os [detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/)da API.
    | **Grupo de recursos** | O grupo de recursos do Azure que conterá seu recurso de serviços cognitivas. Você pode criar um novo grupo ou adicioná-lo a um grupo já existente. |

    ![Tela de criação de recursos](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Clique em **Criar**.

    ***

## <a name="get-the-keys-for-your-resource"></a>Obter as chaves para seu recurso

Depois que o recurso for criado com êxito, você receberá uma notificação pop-up no canto superior direito da tela. Na notificação, clique em **ir para o recurso** para ver o recurso de serviço cognitiva que você criou. 

![Ir para o recurso de serviço cognitiva](media/cognitive-services-apis-create-account/cog-serv-go-to-resource.png)

No painel início rápido que é aberto, você pode acessar o ponto de extremidade e a chave.

![Obter chave e ponto de extremidade](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Tipos de preço e cobrança

Os tipos de preço (e o valor que você recebe é cobrado) baseiam-se no número de transações que você envia usando suas informações de autenticação. Cada tipo de preço especifica:
* número máximo de transações permitidas por segundo (TPS).
* recursos de serviço habilitados no tipo de preço.
* O custo de um número predefinido de transações. Ficar acima desse valor causará um encargo extra, conforme especificado nos [detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) do seu serviço.

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
