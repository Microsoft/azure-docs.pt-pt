---
title: Referência de domínio pré-construída - LUIS
titleSuffix: Azure Cognitive Services
description: Referência para os domínios pré-construídos, que são coleções pré-construídas de intenções e entidades da Language Understanding Intelligent Services (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 73b279f98011181b329cdb010041022ab0da57f0
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018638"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Referência de domínio pré-incorporada para a sua app LUIS
Esta referência fornece informações sobre os [domínios pré-construídos,](./howto-add-prebuilt-models.md)que são coleções pré-construídas de intenções e entidades que a LUIS oferece.

[Domínios personalizados,](luis-how-to-start-new-app.md)em contraste, começam sem intenções e modelos. Pode adicionar quaisquer intenções e entidades de domínio pré-construído a um modelo personalizado.

## <a name="prebuilt-domains-per-language"></a>Domínios pré-construídos por linguagem

A tabela abaixo resume os domínios atualmente suportados. O apoio ao inglês é geralmente mais completo do que outros.

| Tipo de Entidade       | EN-US      | ZH-CN   | DE    | FR     | ES    | TI      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:|
| Calendário  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
|Comunicação  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| E-mail     | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| HomeAutomation          | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Notas     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Realiza   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Restauração  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| ToDo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Utilitários      | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Meteorologia        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Web    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

Os domínios pré-construídos não são **suportados** em:

* Canadense francês
* Hindi
* Mexicano espanhol

## <a name="next-steps"></a>Próximos passos

Aprenda a [entidade simples.](reference-entity-simple.md)