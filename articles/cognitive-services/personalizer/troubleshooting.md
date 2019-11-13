---
title: Solução de problemas-personalizador
titleSuffix: Azure Cognitive Services
description: Este artigo contém respostas para perguntas frequentes sobre solução de problemas sobre o personalizador.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 5911cba54d7dd0eb1a5621112b41e2fc40fa68eb
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953169"
---
# <a name="personalizer-troubleshooting"></a>Solução de problemas do personalizador

Este artigo contém respostas para perguntas frequentes sobre solução de problemas sobre o personalizador.

## <a name="learning-loop"></a>Loop de aprendizagem

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>O loop de aprendizagem parece não aprender. Como fazer corrigir isso?

O loop de aprendizagem precisa de alguns mil pontos de recompensa para que as chamadas de classificação sejam priorizadas com eficiência. 

Se você não tiver certeza sobre como seu loop de aprendizado está sendo usado no momento, execute uma [avaliação offline](concepts-offline-evaluation.md)e aplique a política de aprendizado corrigida. 

## <a name="next-steps"></a>Passos seguintes

[Configurar a frequência de atualização do modelo](how-to-settings.md#model-update-frequency)