---
title: Resolução de problemas - Personalizer
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
ms.openlocfilehash: be6119d96b89622f45db1099a47e858a5893c2cb
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722258"
---
# <a name="personalizer-troubleshooting"></a>Personalizer resolução de problemas

Este artigo contém respostas para perguntas mais frequentes sobre resolução de problemas sobre Personalizer.

## <a name="learning-loop"></a>Ciclo de aprendizagem

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>O loop de aprendizado parece que não saber mais. Como posso corrigir esta situação?

O loop de aprendizado tem algumas chamadas de recompensa milhares de antes de chamadas de classificação priorizar com eficiência. 

Se tiver a certeza sobre como atualmente está se comportando seu loop de aprendizagem, execute uma [avaliação offline](concepts-offline-evaluation.md)e aplicar a política de aprendizagem corrigido. 

## <a name="next-steps"></a>Passos Seguintes

[Configurar a frequência de atualização do modelo](how-to-settings.md#model-update-frequency)