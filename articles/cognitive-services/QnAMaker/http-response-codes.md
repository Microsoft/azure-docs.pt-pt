---
title: Códigos de resposta de HTTP da API - QnA Maker
titleSuffix: Azure Cognitive Services
description: Compreenda quais códigos de resposta HTTP são devolvidos das APIs do QnA Maker. Isto ajudará a resolver quaisquer erros.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/20/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 0416b282a869536b0cea591ba39d7a3804697bac
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446529"
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
