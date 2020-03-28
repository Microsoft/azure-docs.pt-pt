---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 355fe134939b26c51d6e03368f782845628a6b96
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184567"
---
A aplicação do cliente precisa de saber se uma expressão não é significativa ou adequada para a aplicação. A intenção **None** é adicionada a cada aplicação como parte do processo de criação para determinar se uma expressão não pode ser respondida pela aplicação do cliente.

Se o LUIS devolver a intenção **de nenhuma** intenção de uma expressão, a sua aplicação de cliente pode perguntar se o utilizador quer terminar a conversa ou dar mais instruções para continuar a conversação. 

> [!CAUTION] 
> Não deixe a intenção **de Ninguém** vazia. 

1. Selecione **Intents** (Intenções) no painel esquerdo.

2. Selecione a intenção **None** (Nenhuma). Adicione três declarações que o seu utilizador pode introduzir, mas não são relevantes para a sua aplicação de Recursos Humanos:

    | Expressões de exemplo|
    |--|
    |Cães que ladram são irritantes|
    |Encomendar uma piza para mim|
    |Pinguins no oceano|
