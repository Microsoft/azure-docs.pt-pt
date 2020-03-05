---
title: Referência de domínio pré-construído - LUIS
titleSuffix: Azure Cognitive Services
description: Referência para os domínios criados previamente, que são coleções pré-criados de objetivos e entidades do Language Understanding Intelligent Service (LUIS).
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270605"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Referência de domínio pré-construído para a sua app LUIS
Esta referência fornece informações sobre os [domínios pré-construídos,](luis-how-to-use-prebuilt-domains.md)que são coleções pré-construídas de intenções e entidades que a LUIS oferece.

[Os domínios personalizados,](luis-how-to-start-new-app.md)pelo contrário, começam sem intenções e modelos. Pode adicionar qualquer intenções de domínio pré-criado e entidades para um modelo personalizado.

## <a name="custom-domains-per-language"></a>Domínios personalizados por idioma

A tabela abaixo resume os domínios atualmente suportados. O apoio ao inglês é geralmente mais completo do que outros.

| Tipo de Entidade       | EN-US      | ZH-CN   | DE    | FR     | ES    | TI      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:|
| Calendário  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
|Comunicação  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Email     | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| HomeAutomation          | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Notas     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Locais   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| RestaurantReservation  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Todo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Serviços Públicos      | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Meteorologia        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Web    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

Os domínios pré-construídos não são **suportados** em:

* Canadiano francês
* Hindi
* Mexicano espanhol

## <a name="next-steps"></a>Passos seguintes

Aprenda a [entidade simples.](reference-entity-simple.md)