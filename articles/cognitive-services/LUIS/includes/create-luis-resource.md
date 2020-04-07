---
title: Criar recurso LUIS
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2020
ms.author: dapine
ms.openlocfilehash: 88289686e5b091ef3ec309ee9b54ee0f895c8c22
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754360"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-azure-portal"></a>Criar recursos LUIS no portal Azure

1. Utilize [este link](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) para começar a criar recursos LUIS no portal Azure.

1. Introduza todas as definições necessárias:

    |Nome|Objetivo|
    |--|--|
    |Nome da subscrição| a subscrição que será faturada para o recurso.|
    |Grupo de recursos| Um nome de grupo de recursos personalizado que você escolhe ou cria. Os grupos de recursos permitem-lhe agrupar os recursos do Azure para acesso e gestão.|
    |Nome| Um nome personalizado que escolher, usado como subdomínio personalizado para as suas consultas de autor e ponto final de previsão.|
    |Localização de autoria|A região associada ao seu modelo.|
    |Nível de preços de autoria|O nível de preços determina a transação máxima por segundo e mês.|
    |Localização do tempo de execução|A região associada ao seu tempo de execução de previsão publicada.|
    |Nível de preços de tempo de execução|O nível de preços determina a transação máxima por segundo e mês.|

    > [!div class="mx-imgBorder"]
    > [![Criar o recurso de compreensão da linguagem](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Clique em **Rever + criar** e esperar pela criação do recurso.
1. Depois de ambos os recursos serem criados, ainda no portal Azure, selecione o novo recurso de autoria, depois **quickstarts** para obter o **URL final de** autor e **chave** para a autoria programática.

> [!TIP]
> Para utilizar os recursos, no portal LUIS, [atribuir os recursos.](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps)