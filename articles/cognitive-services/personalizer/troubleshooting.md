---
title: Resolução de problemas
titleSuffix: Azure Cognitive Services
description: Perguntas sobre Personalizer de resolução de problemas pode ser encontrada neste artigo.
author: edjez
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: article
ms.date: 06/15/2019
ms.author: edjez
ms.openlocfilehash: 5136bd295c12c4439a894b4dcf0b868d32ce43ca
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313154"
---
# <a name="personalizer-troubleshooting"></a>Personalizer resolução de problemas

Este artigo contém respostas para perguntas mais frequentes sobre resolução de problemas sobre Personalizer.

## <a name="learning-loop"></a>Ciclo de aprendizagem

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>O loop de aprendizado parece que não saber mais. Como posso corrigir esta situação?

O loop de aprendizado tem algumas chamadas de recompensa milhares de antes de chamadas de classificação priorizar com eficiência. 

Se tiver a certeza sobre como atualmente está se comportando seu loop de aprendizagem, execute uma [avaliação offline](concepts-offline-evaluation.md)e aplicar a política de aprendizagem corrigido. 

## <a name="next-steps"></a>Passos Seguintes

[Configurar a frequência de atualização do modelo](how-to-settings.md#model-update-frequency)