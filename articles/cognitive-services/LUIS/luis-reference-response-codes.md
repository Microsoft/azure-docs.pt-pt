---
title: Códigos de resposta HTTP da API
titleSuffix: Azure
description: Compreender quais códigos de resposta HTTP devolvidos pelas APIs de ponto final e de criação de LUIS
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 18ec59200d3cf820794ac353e38106ad26aca697
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337249"
---
# <a name="common-api-response-codes-and-their-meaning"></a>Códigos de resposta de API comuns e o respetivo significado

O [criação](https://aka.ms/luis-authoring-apis) e [endpoint](https://aka.ms/luis-endpoint-apis) APIs devolvem os códigos de resposta HTTP. Mensagens de resposta inclui informações específicas a um pedido, o código de estado de resposta HTTP é geral. 

## <a name="common-status-codes"></a>Códigos de status comuns
A tabela seguinte lista algumas dos códigos de estado de resposta HTTP mais comuns para o [criação](https://aka.ms/luis-authoring-apis) e [endpoint](https://aka.ms/luis-endpoint-apis) APIs:

|Código|API|Explicação|
|:--|--|--|
|400|Criação de ponto final|parâmetros do pedido estão incorretos, que significa que os parâmetros necessários estão em falta, com formato incorreto ou é demasiado grande|
|400|Criação de ponto final|corpo do pedido está incorreto, que significa que o JSON é demasiado grande, com formato incorreto ou em falta|
|401|Criação de conteúdos|Utilizar chave de subscrição de ponto final, em vez de chave de criação|
|401|Criação de ponto final|chave inválido, um formato incorreto ou está vazio|
|401|Criação de ponto final| a chave não corresponde à região|
|401|Criação de conteúdos|não for o proprietário ou funcionário|
|401|Criação de conteúdos|ordem inválido de chamadas de API|
|403|Criação de ponto final|limite de quota de chave mensal total excedido|
|409|Ponto Final|aplicação ainda está a carregar|
|410|Ponto Final|aplicação tem de ser reestruturar e replicado|
|414|Ponto Final|consulta excede o limite máximo de carateres|
|429|Criação de ponto final|Limite de taxa foi excedido (pedidos/segundo)|

## <a name="next-steps"></a>Passos Seguintes

* REST API [criação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) e [endpoint](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) documentação
