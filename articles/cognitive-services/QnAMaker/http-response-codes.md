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
ms.openlocfilehash: cdfa5212f321cc6ec976ea9df301243acc54a23f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65794851"
---
# <a name="qna-maker-api-http-response-codes"></a>Códigos de resposta de HTTP de API do QnA Maker
O [gestão](https://go.microsoft.com/fwlink/?linkid=2092179) e predição APIs retornam códigos de resposta HTTP. Mensagens de resposta inclui informações específicas a um pedido, o código de estado de resposta HTTP é geral. 

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
