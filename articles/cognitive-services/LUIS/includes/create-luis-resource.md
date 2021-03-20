---
title: Criar recurso LUIS
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/20/2020
ms.author: aahi
ms.openlocfilehash: ee7fd384a198c5eff672b14b6cb479aac26cfe54
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95972522"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-the-azure-portal"></a>Criar recursos LUIS no portal Azure

1. Utilize [este link](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) para começar a criar recursos LUIS no portal Azure.

1. Introduza todas as definições necessárias:

    |Name|Objetivo|
    |--|--|
    |Subscrição | A subscrição que será faturada para o recurso.|
    |Grupo de recursos| Um nome de grupo de recursos personalizado que você escolhe ou cria. Os grupos de recursos permitem-lhe agrupar recursos Azure para acesso e gestão.|
    |Name| Um nome personalizado que escolhe. É usado como subdomínio personalizado para as suas consultas de autoria e previsão.|
    |Localização de autoria|A região associada ao seu modelo.|
    |Nível de preço de autoria|Determina as transações máximas por segundo e mês.|
    |Localização de previsão|A região associada ao seu tempo de fim de prazo de previsão publicado.|
    |Nível de preços de previsão|Determina as transações máximas por segundo e mês.|

    > [!div class="mx-imgBorder"]
    > [![Screenshot que mostra o separador Básico em Criar.](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Selecione **Review + create**, e aguarde a criação do recurso.
1. Depois de ambos os recursos serem criados, ainda no portal Azure, selecione o novo recurso de autoria. Em seguida, selecione **Keys e Endpoint** para obter o **URL de ponto final** de autoria e **chave** para a autoria programática.

> [!TIP]
> Para utilizar os recursos, no portal LUIS, [atribua os recursos.](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps)
