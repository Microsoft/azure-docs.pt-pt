---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.openlocfilehash: 1509d175979bb65c467424db5de967f56825a3f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91545654"
---
A aplicação do cliente precisa de saber se uma expressão não é significativa ou apropriada para a aplicação. A intenção **de Nenhum** é adicionada a cada aplicação como parte do processo de criação para determinar se uma expressão não deve ser respondida pela aplicação do cliente.

Se o LUIS devolver a intenção **de Nenhum** para uma expressão, a sua aplicação do cliente pode perguntar se o utilizador quer terminar a conversa ou dar mais instruções para continuar a conversa.

Se deixar a intenção **de Zero** vazia, uma expressão que deve ser prevista fora do domínio do assunto será prevista numa das intenções de domínio existentes. O resultado é que a aplicação do cliente, como um chat bot, realizará operações incorretas com base numa previsão incorreta.

1. Selecione **Intents** (Intenções) no painel esquerdo.

1. Selecione a intenção **None** (Nenhuma). Adicione três expressões que o seu utilizador pode introduzir, mas não são relevantes para a sua aplicação de encomendas Pizza:

    |`None` palavras exemplo|
    |--|
    |`Barking dogs are annoying`|
    |`Penguins in the ocean`|

    Estes exemplos não devem usar palavras que se esperam no seu domínio sujeito, tais `pizza` `cheese` como, `crust` . . `pickup` `deliver` . .