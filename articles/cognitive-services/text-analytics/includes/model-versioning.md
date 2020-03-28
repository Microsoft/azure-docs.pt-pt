---
title: Versão do modelo
titleSuffix: Azure Cognitive Services
description: Especificar versões de modelo nos pontos finais V3
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 4cd8d0901ce23fb227bb6919dee18f5aa3d063ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77088972"
---
A versão 3 do API text analytics permite-lhe escolher a versão do modelo que é mais atual para os seus dados. Utilize o `model-version` parâmetro opcional para selecionar a versão do modelo que é desejada para os seus pedidos. Se este parâmetro não for especificado, a `latest`API não será a versão estável mais recente. Apesar de poder utilizar a versão mais recente do modelo em qualquer pedido, apenas algumas funcionalidades são atualizadas em cada versão. O quadro abaixo descreve quais as funcionalidades que foram atualizadas em cada versão do modelo:

| Versão modelo           | Funcionalidades atualizadas         | Versão mais recente para:           |
|-------------------------|--------------------------|--------------------------|
| `2020-02-01`            | Reconhecimento de entidades                      | Reconhecimento de entidades                      |
| `2019-10-01`            | Reconhecimento de entidades, análise de sentimento  | Deteção de linguagem, extração de frases-chave, análise de sentimento|


Cada resposta dos pontos finais v3 inclui um `model-version` campo que especifica a versão do modelo que foi utilizada.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
Veja [as novidades](../whats-new.md) para mais detalhes sobre as atualizações para estas versões de modelos.
