---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.openlocfilehash: 5eb2517b4b71b61dd70406f345c005d007d9e3ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91535967"
---
A entidade pattern.any permite localizar dados de forma livre em que o texto da entidade dificulta distinguir o final da entidade do resto da expressão.

Uma aplicação de Recursos Humanos ajuda os colaboradores a encontrar formulários da empresa.

|Expressão|
|--|
|Onde está **HRF-123456**?|
|Quem é o autor de **HRF-123234**?|
|**HRF-456098** está publicado em francês?|

No entanto, cada formulário tem um nome formatado, utilizado na tabela anterior, bem como um nome amigável, como `Request relocation from employee new to the company 2018 version 5`.

As expressões na forma amigável têm o seguinte aspeto:

|Expressão|
|--|
|Onde está **Pedir transferência do colaborador que é novo na empresa 2018 versão 5**?|
|Quem é o autor de **Pedir transferência do colaborador que é novo na empresa 2018 versão 5**?|
|**Pedir transferência do colaborador que é novo na empresa 2018 versão 5** está publicado em francês?|

O comprimento variado inclui palavras que podem confundir o LUIS sobre onde termina a entidade. Utilizar uma entidade Pattern.any num padrão permite especificar o início e o final do nome do formulário, para que o LUIS extraia corretamente o nome do formulário.

|Exemplo de expressão de modelo|
|--|
|Onde está {FormName}[?]|
|Quem é o autor de {FormName}[?]|
|{FormName} está publicado em francês[?]|

Revisão Padrão.qualquer informação [de referência](../reference-entity-pattern-any.md)