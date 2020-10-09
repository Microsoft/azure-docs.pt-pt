---
title: Criar recurso LUIS
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: ebfe7e80577b163218a7bc501fa4e3e9b206fd62
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80879219"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-azure-portal"></a>Criar recursos LUIS no portal Azure

1. Utilize [este link](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) para começar a criar recursos LUIS no portal Azure.

1. Introduza todas as definições necessárias:

    |Nome|Objetivo|
    |--|--|
    |Nome da subscrição| a subscrição que será faturada para o recurso.|
    |Grupo de recursos| Um nome de grupo de recursos personalizado que escolher ou criar. Os grupos de recursos permitem-lhe agrupar recursos Azure para acesso e gestão.|
    |Nome| Um nome personalizado que escolhe, usado como subdomínio personalizado para as suas consultas de autoria e previsão final.|
    |Localização de autoria|A região associada ao seu modelo.|
    |Nível de preço de autoria|O nível de preços determina a transação máxima por segundo e mês.|
    |Localização do tempo de execução|A região associada ao seu tempo de fim de prazo de previsão publicado.|
    |Nível de preços de execução|O nível de preços determina a transação máxima por segundo e mês.|

    > [!div class="mx-imgBorder"]
    > [![Criar o recurso de compreensão linguística](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Clique **em Rever + criar** e esperar que o recurso seja criado.
1. Depois de ambos os recursos serem criados, ainda no portal Azure, selecione o novo recurso de autoria, em seguida, **Quickstarts** para obter o **URL de ponta de** autor e **chave** para a autoria programática.

> [!TIP]
> Para utilizar os recursos, no portal LUIS, [atribua os recursos.](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps)