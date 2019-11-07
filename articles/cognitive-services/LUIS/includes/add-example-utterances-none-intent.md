---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: bbe40083159d433c0b5746834e1c530b23b03851
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648301"
---
O aplicativo cliente precisa saber se um expressão não é significativo ou apropriado para o aplicativo. A intenção **None** é adicionada a cada aplicativo como parte do processo de criação para determinar se um expressão não pode ser respondido pelo aplicativo cliente.

Se LUIS retornar a intenção **None** para um expressão, seu aplicativo cliente poderá perguntar se o usuário deseja encerrar a conversa ou fornecer mais instruções para continuar a conversa. 

Se você deixar a intenção **nenhum** vazia, um expressão que deve ser previsto fora do domínio do assunto será previsto em uma das tentativas de domínio do assunto existente. O resultado é que o aplicativo cliente, como um bot de chat, executará operações incorretas com base em uma previsão incorreta. 

1. Selecione **Intents** (Intenções) no painel esquerdo.

1. Selecione a intenção **None** (Nenhuma). Adicione três expressões que o utilizador poderá introduzir, mas que não são relevantes para a sua aplicação:

    |`None` exemplo declarações|
    |--|
    |`Barking dogs are annoying`|
    |`Order a pizza for me`|
    |`Penguins in the ocean`|
