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
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: e4147fbb27c8538f801f6c49f8b535a283faf50f
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325919"
---
O aplicativo cliente precisa saber se um expressão não é significativo ou apropriado para o aplicativo. A intenção **None** é adicionada a cada aplicativo como parte do processo de criação para determinar se um expressão não deve ser respondido pelo aplicativo cliente.

Se LUIS retornar a intenção **None** para um expressão, seu aplicativo cliente poderá perguntar se o usuário deseja encerrar a conversa ou fornecer mais instruções para continuar a conversa. 

Se você deixar a intenção **nenhum** vazia, um expressão que deve ser previsto fora do domínio do assunto será previsto em uma das tentativas de domínio do assunto existente. O resultado é que o aplicativo cliente, como um bot de chat, executará operações incorretas com base em uma previsão incorreta. 

1. Selecione **Intents** (Intenções) no painel esquerdo.

1. Selecione a intenção **None** (Nenhuma). Adicione três declarações que seu usuário pode inserir, mas que não são relevantes para seu aplicativo de ordem de pizza:

    |`None` exemplo declarações|
    |--|
    |`Barking dogs are annoying`|
    |`Order a pizza for me`|
    |`Penguins in the ocean`|

    Esses exemplos não devem usar palavras que você espera em seu domínio de assunto, como `pizza`, `cheese`, `crust``pickup` `deliver`.