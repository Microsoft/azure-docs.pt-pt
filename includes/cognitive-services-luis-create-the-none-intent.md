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
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56738901"
---
A aplicação cliente tem de saber se uma expressão não é significativo ou adequado para a aplicação. O **None** intenção é adicionada a cada aplicação como parte do processo de criação para determinar se uma expressão não pode ser respondida pela aplicação de cliente.

Se o LUIS devolve os **None** intenção para uma expressão, a aplicação cliente pode perguntar se o usuário deseja terminar a conversa ou dar mais instruções para continuar a conversa. 

> [!CAUTION] 
> Não deixe a **None** intenção vazia. 

1. Selecione **Intents** (Intenções) no painel esquerdo.

2. Selecione a intenção **None** (Nenhuma). Adicione três expressões com que o usuário poderá introduzir mas não são relevante para a sua aplicação de recursos humanos:

    | Expressões de exemplo|
    |--|
    |Cães que ladram são irritantes|
    |Encomendar uma piza para mim|
    |Pinguins no oceano|
