---
title: Gerenciar conta e chaves-LUIS
titleSuffix: Azure Cognitive Services
description: As duas informações cruciais para uma conta de LUIS são a conta de utilizador e a chave de criação. Suas informações de logon são gerenciadas em account.microsoft.com. Sua chave de criação é gerenciada na página de configurações do portal do LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 9164c94886488ac7dcadeb7894453daad5d8b322
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560492"
---
# <a name="manage-account-and-authoring-key"></a>Gerir conta e chave de criação

As duas informações cruciais para uma conta de LUIS são a conta de utilizador e a chave de criação. Suas informações de início de sessão são gerenciadas ao [account.microsoft.com](https://account.microsoft.com). Sua chave de criação é gerenciada na página de **configurações** do portal do [Luis](luis-reference-regions.md) .

## <a name="authoring-key"></a>Chave de criação

Essa única chave de criação específica da região, na página **configurações** , permite que você crie todos os seus aplicativos do portal do [Luis](luis-reference-regions.md) , bem como as [APIs de criação](https://go.microsoft.com/fwlink/?linkid=2092087). Como uma conveniência, a chave de criação tem permissão para efetuar uma [limitado](luis-boundaries.md) consulta o número de ponto final de cada mês.

[![Página de definições de LUIS](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

A chave de criação é utilizada para todas as aplicações que é proprietário, bem como todas as aplicações que está listado como um colaborador.

## <a name="authoring-key-regions"></a>Regiões de chave de criação

A chave de criação é específica para o [criação região](luis-reference-regions.md#publishing-regions). A chave não funciona numa região diferente.

## <a name="reset-authoring-key"></a>Repor a chave de criação

Se a sua chave de criação for comprometido, repor a chave. A chave é redefinida em todos os seus aplicativos no portal do [Luis](luis-reference-regions.md) . Se cria seus aplicativos por meio das APIs de criação, tem de alterar o valor de `Ocp-Apim-Subscription-Key` para a nova chave.

## <a name="delete-account"></a>Eliminar conta

Ver [armazenamento de dados e remoção](luis-concept-data-storage.md#accounts) para obter informações sobre quais dados são eliminados quando eliminar a sua conta.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre sua [chave de criação](luis-concept-keys.md#authoring-key).

