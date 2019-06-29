---
title: Criar uma conta dos serviços cognitivos no portal do Azure
titlesuffix: Azure Cognitive Services
description: Como criar uma conta de APIs serviços cognitivos do Azure no portal do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: aahi
ms.openlocfilehash: b857ee0395c447c8699b8f6a812853528812a7bd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445863"
---
# <a name="create-a-cognitive-services-account-using-the-azure-portal"></a>Criar uma conta dos serviços cognitivos no portal do Azure

Neste início rápido, irá aprender como inscrever-se para os serviços cognitivos do Azure e criar uma conta que tenha uma subscrição de serviço único ou múltiplos serviço. Estes serviços são representados pelo Azure [recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), que permitem-lhe ligar a uma ou muitas das APIs serviços cognitivos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure válida. [Criar uma conta](https://azure.microsoft.com/free/) gratuitamente.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>Criar um novo recurso de serviços cognitivos do Azure

Antes de criar um recurso, tem de ter um grupo de recursos do Azure. Cada conta dos serviços cognitivos (e seu recurso do Azure associado) têm de pertencer a um grupo de recursos do Azure. Quando criar uma conta, terá a opção para criar um novo grupo de recursos ou utilize um já existente. Este artigo mostra como criar um novo grupo de recursos.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e clique em **+ criar um recurso**.

    ![Selecione as APIs serviços cognitivos](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Pode encontrar os serviços cognitivos disponível das seguintes formas:
    * Utilize a barra de pesquisa e introduza o nome do serviço que pretende subscrever.
        * Para criar um recurso para uma subscrição de múltiplos serviço, introduza **dos serviços cognitivos** na pesquisa de barra e selecione o **dos serviços cognitivos** recursos.

        ![Procure os serviços cognitivos](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    * Para ver todos os serviços cognitivos disponíveis, selecione **IA + Machine Learning**, em **Azure Marketplace**. Se não vir o serviço estiver interessado em, clique em **ver tudo** e desloque-se para **dos serviços cognitivos**. Clique em **mais** para ver todo o catálogo de APIs serviços cognitivos.
    
        ![Selecione as APIs serviços cognitivos](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. Sobre o **criar** página, forneça as seguintes informações:

    > [!IMPORTANT]
    > Lembre-se a localização do Azure, pois talvez tenha ao chamar os serviços cognitivos do Azure.

    |    |    |
    |--|--|
    | **Nome** | Um nome descritivo para o seu recurso de serviços cognitivos. Recomendamos que utilize um nome descritivo, por exemplo *MyCognitiveServicesAccount*. |
    | **Subscrição** | Selecione uma das suas subscrições do Azure disponíveis. |
    | **Location** | A localização da sua instância de serviço cognitivo. Localizações diferentes podem introduzir a latência, mas não tiver nenhum impacto sobre a disponibilidade de tempo de execução do seu recurso. |
    | **Escalão de preço** | O custo da sua conta dos serviços cognitivos depende das opções que escolher e a sua utilização. Para obter mais informações, consulte a API [os detalhes dos preços](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Grupo de recursos** | O [grupo de recursos do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) que irá conter o seu recurso dos serviços cognitivos. Pode criar um novo grupo ou adicioná-lo a um grupo já existente. |

    ![Ecrã de criação de recursos](media/cognitive-services-apis-create-account/resource_create_screen.png)


## <a name="get-the-keys-for-your-subscription"></a>Obter as chaves para a sua subscrição

Depois de criar o seu recurso, pode acessá-lo a partir do dashboard do Azure se o tiver afixado. Caso contrário, pode encontrá-lo na **grupos de recursos**. Depois de selecionar o seu recurso, pode obter as chaves selecionando **chaves** sob **gestão de recursos**.

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser limpar e remover uma subscrição de serviços cognitivos, é possível eliminar o recurso ou grupo de recursos. Eliminar o grupo de recursos também elimina a quaisquer outros recursos associados ao grupo de recursos.

Para remover um grupo de recursos através do portal do Azure:

1. No portal do Azure, expanda o menu no lado esquerdo para abrir o menu de serviços e escolha **Grupos de Recursos**, para apresentar a lista dos seus grupos de recursos.
2. Localize o grupo de recursos a eliminar e, faça duplo clique no botão de mais (...) no lado direito da lista.
3. Selecione **Eliminar grupo de recursos** e confirme.

## <a name="see-also"></a>Consulte também

* [Autenticar pedidos para os serviços cognitivos do Azure](authentication.md)
* [Quais são os serviços cognitivos do Azure?](Welcome.md)
* [Suporte de linguagem natural](language-support.md)
* [Suporte de contentor do docker](cognitive-services-container-support.md)
