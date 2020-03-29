---
title: Códigos de resposta API HTTP - LUIS
titleSuffix: Azure Cognitive Services
description: Entenda quais os códigos de resposta HTTP devolvidos das APIs de Autoria luis e endpoint
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 405381a2d80ee6469b2b90bcb25923004fe55882
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270253"
---
# <a name="common-api-response-codes-and-their-meaning"></a>Códigos comuns de resposta da API e o seu significado

As APIs de [autor](https://go.microsoft.com/fwlink/?linkid=2092087) e [ponto final](https://go.microsoft.com/fwlink/?linkid=2092356) devolvem códigos de resposta HTTP. Embora as mensagens de resposta incluam informações específicas de um pedido, o código de estado de resposta HTTP é geral.

## <a name="common-status-codes"></a>Códigos de estado comuns
A tabela que se segue enumera alguns dos códigos de estado de resposta http mais comuns para as APIs [de autor](https://go.microsoft.com/fwlink/?linkid=2092087) e [ponto final:](https://go.microsoft.com/fwlink/?linkid=2092356)

|Código|API|Explicação|
|:--|--|--|
|400|Autoria, Ponto Final|os parâmetros do pedido são incorretos, o que significa que os parâmetros necessários estão em falta, mal formados ou muito grandes|
|400|Autoria, Ponto Final|o corpo do pedido está incorreto, o que significa que o JSON está em falta, mal formado ou muito grande|
|401|Criação|chave de subscrição de ponto final usado, em vez de chave de autoria|
|401|Autoria, Ponto Final|chave inválida, mal formada ou vazia|
|401|Autoria, Ponto Final| chave não corresponde à região|
|401|Criação|você não é o proprietário ou colaborador|
|401|Criação|ordem inválida de chamadas API|
|403|Autoria, Ponto Final|limite total mensal de quota-chave ultrapassado|
|409|Ponto Final|aplicação ainda está carregando|
|410|Ponto Final|aplicação precisa ser retreinado e republicado|
|414|Ponto Final|consulta excede o limite máximo de caracteres|
|429|Autoria, Ponto Final|O limite de taxa é ultrapassado (pedidos/segundo)|

## <a name="next-steps"></a>Passos seguintes

* Documento de [autor e](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) ponto [final](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) da API
