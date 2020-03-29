---
title: Referência de domínio pré-construído - LUIS
titleSuffix: Azure Cognitive Services
description: Referência para os domínios pré-construídos, que são coleções pré-construídas de intenções e entidades de Serviços Inteligentes de Compreensão de Línguas (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 05ad340b3856291832ba0521c7da70ad55260384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270605"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Referência de domínio pré-construído para a sua app LUIS
Esta referência fornece informações sobre os [domínios pré-construídos,](luis-how-to-use-prebuilt-domains.md)que são coleções pré-construídas de intenções e entidades que a LUIS oferece.

[Os domínios personalizados,](luis-how-to-start-new-app.md)pelo contrário, começam sem intenções e modelos. Pode adicionar quaisquer intenções e entidades de domínio pré-construídas a um modelo personalizado.

## <a name="custom-domains-per-language"></a>Domínios personalizados por idioma

A tabela abaixo resume os domínios atualmente suportados. O apoio ao inglês é geralmente mais completo do que outros.

| Tipo de Entidade       | EN-US      | ZH-CN   | DE    | FR     | ES    | TI      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:|
| Calendário  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
|Comunicação  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Email     | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Domótica          | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Notas     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Locais   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Restauração Reserva  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Todo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Utilitários      | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Meteorologia        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Web    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

Os domínios pré-construídos não são **suportados** em:

* Canadiano francês
* Hindi
* Mexicano espanhol

## <a name="next-steps"></a>Passos seguintes

Aprenda a [entidade simples.](reference-entity-simple.md)