---
title: Códigos de resposta de HTTP da API - QnA Maker
titleSuffix: Azure Cognitive Services
description: Compreenda quais códigos de resposta HTTP são devolvidos das APIs do QnA Maker. Isto ajudará a resolver quaisquer erros.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/20/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 319623487f37308b5b8fe3fd107b01733825184d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60233333"
---
# <a name="qna-maker-api-http-response-codes"></a>Códigos de resposta de HTTP de API do QnA Maker
O [gestão](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) e predição APIs retornam códigos de resposta HTTP. Mensagens de resposta inclui informações específicas a um pedido, o código de estado de resposta HTTP é geral. 

### <a name="management"></a>Gestão

|Código|Explicação|
|:--|--|
|2xx|Êxito|
|400|parâmetros do pedido estão incorretos, que significa que os parâmetros necessários estão em falta, com formato incorreto ou é demasiado grande|
|400|corpo do pedido está incorreto, que significa que o JSON é demasiado grande, com formato incorreto ou em falta|
|401|Chave inválida|
|403|Proibido - não tem permissões corretas|
|404|Não existe KB|
|410|Esta API foi preterido e já não está disponível|
