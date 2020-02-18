---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 155c88ec4766391f70701b17038b915c399d8b0c
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77371839"
---
## <a name="sign-in-to-luis-portal"></a>Inscreva-se no portal LUIS

Um novo utilizador da LUIS precisa seguir este procedimento:

1. Inscreva-se no [portal LUIS (pré-visualização)](https://preview.luis.ai), selecione o seu país e concorde com os termos de utilização. Se vir **as Minhas Apps** em vez disso, já existe um recurso LUIS e deverá avançar para criar uma app.

1. Selecione **o recurso Create Azure** e, em seguida, selecione **Criar um recurso de autoria para migrar as suas apps para.**

    ![Escolha um tipo de recurso de autor de compreensão linguística](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Preencha os detalhes para o recurso.

    ![Criar recurso de autoria](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    Ao **criar um novo recurso de autoria,** forneça as seguintes informações:

    * **Nome** de recurso - um nome personalizado que escolher, usado como parte do URL para as suas consultas de autor e ponto final de previsão.
    * **Inquilino** - o inquilino com quem a sua assinatura Azure está associada.
    * Nome de **subscrição** - a subscrição que será faturada para o recurso.
    * **Grupo de recursos** - um nome de grupo de recursos personalizado que você escolhe ou cria. Os grupos de recursos permitem-lhe agrupar os recursos do Azure para acesso e gestão.
    * **Localização** - a escolha da localização baseia-se na seleção do **grupo de recursos.**
    * **Nível** de preços - o nível de preços determina a transação máxima por segundo e mês.

1. É apresentado um resumo do recurso a criar. Selecione **Seguinte**.

    ![Criar recurso de autoria](../media/sign-in/sign-in-confirm-key-selection.png)

1. Confirme selecionando **Continuar**.

    ![Criar recurso de autoria](../media/sign-in/sign-in-confirm-continue.png)