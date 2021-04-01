---
title: Códigos de resposta API HTTP - LUIS
titleSuffix: Azure Cognitive Services
description: Entenda quais os códigos de resposta HTTP devolvidos das APIs de Autoria e Endpoint da LUIS
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 03/04/2019
ms.openlocfilehash: 46a7fff86d3a8c4539b77a0f271179d68e7a26d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "91541684"
---
# <a name="common-api-response-codes-and-their-meaning"></a>Códigos de resposta comuns da API e o seu significado

As APIs [de autoria](https://go.microsoft.com/fwlink/?linkid=2092087) e [ponto final](https://go.microsoft.com/fwlink/?linkid=2092356) devolvem códigos de resposta HTTP. Embora as mensagens de resposta incluam informações específicas a um pedido, o código de estado de resposta HTTP é geral.

## <a name="common-status-codes"></a>Códigos de estado comum
A tabela a seguir enumera alguns dos códigos de estado de resposta HTTP mais comuns para as APIs [de autoria](https://go.microsoft.com/fwlink/?linkid=2092087) e [ponto final:](https://go.microsoft.com/fwlink/?linkid=2092356)

|Código|API|Explicação|
|:--|--|--|
|400|Autoria, Endpoint|parâmetros do pedido são incorretos, o que significa que os parâmetros necessários estão faltando, mal formados, ou muito grande|
|400|Autoria, Endpoint|corpo do pedido é incorreto o que significa que o JSON está faltando, mal formado, ou muito grande|
|401|Criação|chave de subscrição de ponto final usado, em vez de chave de autoria|
|401|Autoria, Endpoint|chave inválida, mal formada ou vazia|
|401|Autoria, Endpoint| chave não corresponde região|
|401|Criação|você não é o proprietário ou colaborador|
|401|Criação|ordem inválida de chamadas API|
|403|Autoria, Endpoint|limite total de quota-chave mensal ultrapassado|
|409|Ponto final|aplicação ainda está a carregar|
|410|Ponto final|aplicação precisa ser retreinada e republicada|
|414|Ponto final|consulta excede limite máximo de caracteres|
|429|Autoria, Endpoint|Limite de taxa é ultrapassado (pedidos/segundo)|

## <a name="next-steps"></a>Passos seguintes

* Documentação [de autoria](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) e [endpoint](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) REST API
