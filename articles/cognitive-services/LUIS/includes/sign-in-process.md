---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/05/2020
ms.topic: include
ms.openlocfilehash: fda9df6c7e9651bbd3b0b70ad9d47f23c0c19d01
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541441"
---
## <a name="sign-in-to-luis-portal"></a>Iniciar sessão no portal LUIS

Um novo utilizador da LUIS precisa de seguir este procedimento:

1. Inscreva-se no [portal LUIS,](https://www.luis.ai)selecione o seu país/região e concorde com os termos de utilização. Se vir **as Minhas Apps** em vez disso, já existe um recurso LUIS e deverá adiantar-se para criar uma aplicação. Para regiões apoiadas, visite [regiões de autoria e publicação e as chaves associadas.](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions)

1. Selecione **Criar recurso Azure** e, em seguida, selecione **Criar um recurso de autoria para migrar as suas apps para.**

    ![Escolha um tipo de recurso de autoria de compreensão linguística](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Preencha os detalhes do recurso.

    ![A screenshot mostra ao Criar um novo recurso para o painel de autoria.](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    Ao **criar um novo recurso de autoria,** forneça as seguintes informações:

    * **Nome do recurso** - um nome personalizado que você escolhe, usado como parte do URL para as suas consultas de autoria e previsão de ponto final.
    * **Inquilino** - o inquilino a que a sua assinatura Azure está associada.
    * **Nome da assinatura** - a subscrição que será faturada para o recurso.
    * **Grupo de recursos** - um nome de grupo de recursos personalizado que você escolhe ou cria. Os grupos de recursos permitem-lhe agrupar recursos Azure para acesso e gestão.
    * **Localização** - a escolha da localização baseia-se na seleção do **grupo de recursos.**
    * **Nível de preços** - o nível de preços determina a transação máxima por segundo e mês.

1. É apresentado um resumo do recurso a criar. Selecione **Seguinte**.

    ![A screenshot mostra a página Welcome com a opção de ligar à sua conta Azure.](../media/sign-in/sign-in-confirm-key-selection.png)

1. Confirme selecionando **Continue**.

    ![A screenshot mostra a página Welcome depois de ter ligado à sua conta Azure.](../media/sign-in/sign-in-confirm-continue.png)
