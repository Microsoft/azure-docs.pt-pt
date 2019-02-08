---
title: Gerir definições
titleSuffix: Language Understanding - Azure Cognitive Services
description: Utilize o site do LUIS para gerir as definições de conta de utilizador e a chave de criação utilizada em todas as suas aplicações.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 9fc1882a56c0e1bccdfbb658dac83e4c231ef261
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879093"
---
# <a name="manage-account-and-authoring-key"></a>Gerir conta e chave de criação
As duas informações cruciais para uma conta de LUIS são a conta de utilizador e a chave de criação. Suas informações de início de sessão são gerenciadas ao [account.microsoft.com](https://account.microsoft.com). A chave de criação é gerenciada a partir da [LUIS](luis-reference-regions.md) site **definições** página. 

## <a name="authoring-key"></a>Chave de criação

Esse único e específico da região de criação chave, no **definições** página, permite-lhe criar todas as suas aplicações a partir os [LUIS](luis-reference-regions.md) Web site, bem como o [APIs de criação](https://aka.ms/luis-authoring-api). Como uma conveniência, a chave de criação tem permissão para efetuar uma [limitado](luis-boundaries.md) consulta o número de ponto final de cada mês. 

[![Página de definições de LUIS](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

A chave de criação é utilizada para todas as aplicações que é proprietário, bem como todas as aplicações que está listado como um colaborador.

## <a name="authoring-key-regions"></a>Regiões de chave de criação
A chave de criação é específica para o [criação região](luis-reference-regions.md#publishing-regions). A chave não funciona numa região diferente. 

## <a name="reset-authoring-key"></a>Repor a chave de criação
Se a sua chave de criação for comprometido, repor a chave. A chave é reposta em todas as suas aplicações no [LUIS](luis-reference-regions.md) Web site. Se cria seus aplicativos por meio das APIs de criação, tem de alterar o valor de `Ocp-Apim-Subscription-Key` para a nova chave. 

## <a name="delete-account"></a>Eliminar conta
Ver [armazenamento de dados e remoção](luis-concept-data-storage.md#accounts) para obter informações sobre quais dados são eliminados quando eliminar a sua conta. 

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre sua [chave de criação](luis-concept-keys.md#authoring-key). 

