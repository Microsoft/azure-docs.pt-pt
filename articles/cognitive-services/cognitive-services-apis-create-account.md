---
title: Criar uma conta dos serviços cognitivos no portal do Azure
titlesuffix: Azure Cognitive Services
description: Como criar uma conta de APIs serviços cognitivos da Microsoft no portal do Azure.
services: cognitive-services
author: garyericson
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: garye
ms.openlocfilehash: 21e1eb00f688f2e7061e876cc4375d6d780838fc
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534808"
---
# <a name="quickstart-create-a-cognitive-services-account-in-the-azure-portal"></a>Início rápido: Criar uma conta dos serviços cognitivos no portal do Azure

Neste início rápido, aprenderá a inscrever-se os serviços cognitivos do Azure e criar uma subscrição de serviço único ou múltiplos serviço. Estes serviços são representados pelo Azure [recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), que permitem-lhe ligar a uma ou muitas das APIs serviços cognitivos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure válida. [Criar uma conta](https://azure.microsoft.com/free/) gratuitamente.

## <a name="create-and-subscribe-to-an-azure-cognitive-services-resource"></a>Criar e subscrever um recurso de serviços cognitivos do Azure

Antes de começar, é importante saber que existem dois tipos de subscrições de serviços cognitivos do Azure. A primeira é uma subscrição para um único serviço, como a visão do computador ou dos serviços de voz. Uma subscrição de serviço único é restrita a esse recurso. O segundo é uma assinatura múltiplos serviço para os serviços cognitivos do Azure. Esta subscrição permite-lhe utilizar uma única subscrição para a maioria dos serviços cognitivos do Azure. Esta opção também consolida a faturação. Ver [preços dos serviços cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) para obter informações adicionais.

>[!WARNING]
> Neste momento, estes serviços **não** chaves múltiplos serviços de suporte: A ferramenta QnA Maker, serviços de voz e visão personalizada.

As secções seguintes explicam como criar uma subscrição única ou múltiplos serviço.

### <a name="single-service-subscription"></a>Subscrição de serviço único

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e clique em **+ criar um recurso**.

    ![Selecione as APIs serviços cognitivos](media/cognitive-services-apis-create-account/azurePortalScreen.png)

2. No Azure Marketplace, selecione **IA + Machine Learning**. Se não vir o serviço estiver interessado em, clique em **ver todas as** para ver todo o catálogo de APIs serviços cognitivos.

    ![Selecione as APIs serviços cognitivos](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. Sobre o **criar** página, forneça as seguintes informações:

    |    |    |
    |--|--|
    | **Nome** | Um nome descritivo para o seu recurso de serviços cognitivos. Recomendamos que utilize um nome descritivo, por exemplo *MyNameFaceAPIAccount*. |
    | **Subscrição** | Selecione uma das suas subscrições do Azure disponíveis. |
    | **Localização** | A localização da sua instância de serviço cognitivo. Localizações diferentes podem introduzir a latência, mas não tiver nenhum impacto sobre a disponibilidade de tempo de execução do seu recurso. |
    | **Escalão de preço** | O custo da sua conta dos serviços cognitivos depende das opções que escolher e a sua utilização. Para obter mais informações, consulte a API [os detalhes dos preços](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Grupo de recursos** | O [grupo de recursos do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group) que irá conter o seu recurso dos serviços cognitivos. Pode criar um novo grupo ou adicioná-lo a um grupo já existente. |

    ![Ecrã de criação de recursos](media/cognitive-services-apis-create-account/resource_create_screen.png)

### <a name="multi-service-subscription"></a>Subscrição múltiplos serviço

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e clique em **+ criar um recurso**.

    ![Selecione as APIs serviços cognitivos](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Localize a barra de pesquisa e introduza: **Os serviços cognitivos**.

    ![Procure os serviços cognitivos](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

3. Selecione **dos serviços cognitivos**.

    ![Selecione os serviços cognitivos](media/cognitive-services-apis-create-account/azureMarketplaceMulti.png)

3. Sobre o **criar** página, forneça as seguintes informações:

    |    |    |
    |--|--|
    | **Nome** | Um nome descritivo para o seu recurso de serviços cognitivos. Recomendamos que utilize um nome descritivo, por exemplo *MyCognitiveServicesAccount*. |
    | **Subscrição** | Selecione uma das suas subscrições do Azure disponíveis. |
    | **Localização** | A localização da sua instância de serviço cognitivo. Localizações diferentes podem introduzir a latência, mas não tiver nenhum impacto sobre a disponibilidade de tempo de execução do seu recurso. |
    | **Escalão de preço** | O custo da sua conta dos serviços cognitivos depende das opções que escolher e a sua utilização. Para obter mais informações, consulte a API [os detalhes dos preços](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Grupo de recursos** | O [grupo de recursos do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group) que irá conter o seu recurso dos serviços cognitivos. Pode criar um novo grupo ou adicioná-lo a um grupo já existente. |

    ![Ecrã de criação de recursos](media/cognitive-services-apis-create-account/resource_create_screen_multi.png)

## <a name="access-your-resource"></a>Aceder ao seu recurso

> [!NOTE]
> Proprietários de subscrições podem desativar a criação de contas de serviços cognitivos para subscrições e grupos de recursos através da aplicação [o Azure policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), atribuir uma definição de política de "tipos de recurso não permitidos" e especificando **Microsoft.CognitiveServices/accounts** como o tipo de recurso de destino.

Depois de criar o seu recurso, pode acessá-lo a partir do dashboard do Azure se o tiver afixado. Caso contrário, pode encontrá-lo na **grupos de recursos**.

O recurso de serviços cognitivos, pode utilizar o URL de ponto final e chaves no **descrição geral** secção para começar a criar API chama-se nas suas aplicações.

![Ecrã de recursos](media/cognitive-services-apis-create-account/resourceScreen.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Autenticar pedidos para os serviços cognitivos do Azure](authentication.md)

## <a name="see-also"></a>Consulte também

* [Quickstart: Extrair texto manuscrito a partir de uma imagem](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text)
* [Tutorial: Criar uma aplicação para detetar e rostos numa imagem de fotograma](https://docs.microsoft.com/azure/cognitive-services/Face/Tutorials/FaceAPIinCSharpTutorial)
* [Criar uma página Web de pesquisa personalizada](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)
* [Integrar a compreensão de idiomas (LUIS) com um bot, com o Bot Framework](https://docs.microsoft.com/azure/cognitive-services/luis/luis-nodejs-tutorial-build-bot-framework-sample)
