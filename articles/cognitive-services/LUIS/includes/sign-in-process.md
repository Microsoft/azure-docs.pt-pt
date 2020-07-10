---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/05/2020
ms.topic: include
ms.author: diberry
ms.openlocfilehash: ef7208596ead8f7d3903bb614ccb980df782e581
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837868"
---
## <a name="sign-in-to-luis-portal"></a>Inscreva-se no portal LUIS

Um novo utilizador da LUIS precisa de seguir este procedimento:

1. Inscreva-se no [portal LUIS,](https://www.luis.ai)selecione o seu país/região e concorde com os termos de utilização. Se vir **as Minhas Apps** em vez disso, já existe um recurso LUIS e deverá adiantar-se para criar uma aplicação. Para regiões apoiadas, visite [regiões de autoria e publicação e as chaves associadas.](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions)

1. Selecione **Criar recurso Azure** e, em seguida, selecione **Criar um recurso de autoria para migrar as suas apps para.**

    ![Escolha um tipo de recurso de autoria de compreensão linguística](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Preencha os detalhes do recurso.

    ![Criar recurso de autoria](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    Ao **criar um novo recurso de autoria,** forneça as seguintes informações:

    * **Nome do recurso** - um nome personalizado que você escolhe, usado como parte do URL para as suas consultas de autoria e previsão de ponto final.
    * **Inquilino** - o inquilino a que a sua assinatura Azure está associada.
    * **Nome da assinatura** - a subscrição que será faturada para o recurso.
    * **Grupo de recursos** - um nome de grupo de recursos personalizado que você escolhe ou cria. Os grupos de recursos permitem-lhe agrupar recursos Azure para acesso e gestão.
    * **Localização** - a escolha da localização baseia-se na seleção do **grupo de recursos.**
    * **Nível de preços** - o nível de preços determina a transação máxima por segundo e mês.

1. É apresentado um resumo do recurso a criar. Selecione **Seguinte**.

    ![Criar recurso de autoria](../media/sign-in/sign-in-confirm-key-selection.png)

1. Confirme selecionando **Continue**.

    ![Criar recurso de autoria](../media/sign-in/sign-in-confirm-continue.png)
