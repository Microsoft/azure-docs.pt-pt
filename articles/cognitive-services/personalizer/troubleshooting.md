---
title: Solução de problemas-personalizador
titleSuffix: Azure Cognitive Services
description: Perguntas sobre solução de problemas sobre o personalizador podem ser encontradas neste artigo.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 9f4c4129217923f7fb32996f7447ed09a034f888
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955214"
---
# <a name="personalizer-troubleshooting"></a>Solução de problemas do personalizador

Este artigo contém respostas para perguntas frequentes sobre solução de problemas sobre o personalizador.

## <a name="learning-loop"></a>Loop de aprendizagem

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>O loop de aprendizagem parece não aprender. Como posso corrigir isto?

O loop de aprendizagem precisa de alguns mil pontos de recompensa para que as chamadas de classificação sejam priorizadas com eficiência. 

Se você não tiver certeza sobre como seu loop de aprendizado está sendo usado no momento, execute uma [avaliação offline](concepts-offline-evaluation.md)e aplique a política de aprendizado corrigida. 

## <a name="next-steps"></a>Passos Seguintes

[Configurar a frequência de atualização do modelo](how-to-settings.md#model-update-frequency)