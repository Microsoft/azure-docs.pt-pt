---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 1effd07cef439a6257028549b7b7114b742eb478
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488832"
---
## <a name="sign-in-to-luis-portal"></a>Entrar no portal do LUIS

Um novo usuário para LUIS precisa seguir este procedimento:

1. Entre no [portal do Luis](https://www.luis.ai), selecione seu país e concorde com os termos de uso.
1. Selecione **criar recurso do Azure** e, em seguida, selecione **criar um recurso de criação para o qual migrar seus aplicativos.**

    ![Escolha um tipo de Reconhecimento vocal recurso de criação](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Preencha os detalhes do recurso.

    ![Criar recurso de criação](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    Ao **criar um novo recurso de criação**, forneça as seguintes informações: 

    * **Nome do recurso** -um nome personalizado que você escolhe, usado como parte da URL para suas consultas de ponto de extremidade de criação e previsão.
    * **Locatário** -o locatário ao qual sua assinatura do Azure está associada. 
    * **Nome da assinatura** -a assinatura que será cobrada para o recurso.
    * **Grupo de recursos** -um nome de grupo de recursos personalizado que você escolhe ou cria. Os grupos de recursos permitem que você agrupe recursos do Azure para acesso e gerenciamento. 
    * **Local** -a escolha de local é baseada na seleção do **grupo de recursos** .
    * **Tipo de preço** – o tipo de preço determina a transação máxima por segundo e mês.

1. É exibido um resumo do recurso a ser criado. Selecione **Seguinte**.

    ![Criar recurso de criação](../media/sign-in/sign-in-confirm-key-selection.png)

1. Confirme selecionando **continuar**. 

    ![Criar recurso de criação](../media/sign-in/sign-in-confirm-continue.png)