---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 10/30/2020
ms.topic: include
ms.openlocfilehash: e592c11062e81d48014a90895a0e42b460d4b77c
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128239"
---
## <a name="sign-in-to-luis-portal"></a>Iniciar sessão no portal LUIS

Um novo utilizador da LUIS precisa de seguir este procedimento:

1. Inscreva-se no [portal LUIS,](https://www.luis.ai)selecione o seu país/região e concorde com os termos de utilização. Se vir **as Minhas Apps** em vez disso, já existe um recurso LUIS e deverá adiantar-se para criar uma aplicação. Terá duas opções de inscrição:

    * Utilizando um recurso Azure (recomendado) - permite-lhe ligar a sua conta LUIS a um recurso de autoria Azure novo ou existente. Isto equivale a inscrever-se já migrado. Não precisará passar pelo [processo de migração](../luis-migration-authoring.md#what-is-migration) mais tarde.

    * Usando uma chave de teste. Isto permite-lhe iniciar sação no LUIS com um recurso experimental que não precisa de configurar. Se escolher esta opção, acabará por ser obrigado a [migrar](../luis-migration-authoring.md#migration-steps) a sua conta e a ligar as suas aplicações a um recurso de autoria.

1. Na janela **de autoria Escolha uma** janela de autoria que apareça, encontre a sua assinatura Azure e recurso de autoria LUIS. Se não tiver um recurso, pode criar um novo.

    :::image type="content" source="../media/luis-how-to-azure-subscription/choose-authoring-resource.png" alt-text="Escolha um tipo de recurso de autoria de compreensão linguística.":::
    
    Quando criar um novo recurso de autoria, forneça as seguintes informações:
    * **Nome do inquilino** - o inquilino a que a sua assinatura Azure está associada.
    * **Nome de subscrição Azure** - a subscrição que será faturada para o recurso.
    * **Nome do grupo de recursos Azure** - um nome de grupo de recursos personalizado que você escolhe ou cria. Os grupos de recursos permitem-lhe agrupar recursos Azure para acesso e gestão.
    * **Nome de recurso Azure** - um nome personalizado que você escolhe, usado como parte do URL para as suas consultas de autoria e previsão de ponto final.
    * **Nível de preços** - o nível de preços determina a transação máxima por segundo e mês.


