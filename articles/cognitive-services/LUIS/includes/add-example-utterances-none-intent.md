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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77279671"
---
A aplicação do cliente precisa de saber se uma expressão não é significativa ou adequada para a aplicação. A intenção **None** é adicionada a cada aplicação como parte do processo de criação para determinar se uma expressão não deve ser respondida pela aplicação do cliente.

Se o LUIS devolver a intenção **de nenhuma** intenção de uma expressão, a sua aplicação de cliente pode perguntar se o utilizador quer terminar a conversa ou dar mais instruções para continuar a conversação.

Se deixar a intenção **de "Nenhum"** vazia, uma expressão que deve ser prevista fora do domínio sujeito será prevista numa das intenções de domínio do sujeito existentes. O resultado é que a aplicação do cliente, como um chat bot, irá realizar operações incorretas com base numa previsão incorreta.

1. Selecione **Intents** (Intenções) no painel esquerdo.

1. Selecione a intenção **None** (Nenhuma). Adicione três declarações que o seu utilizador pode introduzir, mas não são relevantes para a sua aplicação de encomenda pizza:

    |`None`expressões exemplo|
    |--|
    |`Barking dogs are annoying`|
    |`Penguins in the ocean`|

    Estes exemplos não devem usar palavras que `pizza`se `cheese` `crust`espera `pickup` `deliver`no seu domínio de assunto, tais como, .