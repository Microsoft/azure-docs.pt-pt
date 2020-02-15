---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.author: diberry
ms.openlocfilehash: 956aa308bf1cb3736c491031239661ec6b295ddb
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77279671"
---
O aplicativo cliente precisa saber se um expressão não é significativo ou apropriado para o aplicativo. A intenção **None** é adicionada a cada aplicação como parte do processo de criação para determinar se uma expressão não deve ser respondida pela aplicação do cliente.

Se o LUIS devolver a intenção **de nenhuma** intenção de uma expressão, a sua aplicação de cliente pode perguntar se o utilizador quer terminar a conversa ou dar mais instruções para continuar a conversação.

Se deixar a intenção **de "Nenhum"** vazia, uma expressão que deve ser prevista fora do domínio sujeito será prevista numa das intenções de domínio do sujeito existentes. O resultado é que o aplicativo cliente, como um bot de chat, executará operações incorretas com base em uma previsão incorreta.

1. Selecione **Intents** (Intenções) no painel esquerdo.

1. Selecione a intenção **None** (Nenhuma). Adicione três declarações que seu usuário pode inserir, mas que não são relevantes para seu aplicativo de ordem de pizza:

    |`None` exemplo de expressões|
    |--|
    |`Barking dogs are annoying`|
    |`Penguins in the ocean`|

    Estes exemplos não devem usar palavras que se esperam no seu domínio de assunto, tais como `pizza`, `cheese`, `crust`, `pickup` `deliver`.