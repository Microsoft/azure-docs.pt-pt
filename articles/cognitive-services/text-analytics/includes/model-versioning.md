---
title: Controle de versão de modelo
titleSuffix: Azure Cognitive Services
description: Especificar versões de modelo nos pontos de extremidade v3
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/25/2019
ms.author: aahi
ms.openlocfilehash: 29850cb9cb40eae0829b5d8c2b58b5f9518f18d5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021160"
---
A versão 3 do API de Análise de Texto permite que você escolha o modelo de Análise de Texto usado em seus dados. Use o parâmetro opcional `model-version` para selecionar uma versão do modelo em suas solicitações. Se esse parâmetro não for especificado, a API usará como padrão `latest`, a versão mais recente do modelo estável.

Versões de modelo disponíveis:
* `2019-10-01` (`latest`)

Cada resposta dos pontos de extremidade v3 inclui um campo `model-version` especificando a versão do modelo que foi usada.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
