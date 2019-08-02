---
title: Conversation Learner configuração padrão-serviços cognitivas da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba mais sobre a configuração de Conversation Learner padrão.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: aaef6f5498e5a8da65d1c829feae8b3e85dba0fd
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705301"
---
# <a name="default-values-and-boundaries"></a>Valores predefinidos e limites

Este documento descreve a configuração padrão de Conversation Learner e os principais limites de serviço.

## <a name="default-configuration"></a>Configuração padrão

Parâmetro | Valor predefinido
--- | --- 
Tempo limite de sessão padrão | 30 minutos

## <a name="boundaries"></a>Limites

Parâmetro | Limite
--- | --- 
API de criação, chamadas HTTP máximas por mês | 5 MIN
API de criação, chamadas HTTP máximas por segundo | 25
API de sessão, chamadas HTTP máximas por mês | 500 K
API de sessão, chamadas HTTP máximas por segundo | 10
Número máximo de entidades personalizadas (não programáticas) por modelo | Consulte [Luis fronteiras doc](https://docs.microsoft.com/azure/cognitive-services/luis/luis-boundaries); na prática, o número real pode ser ligeiramente menor
Número máximo de entidades predefinidas por modelo | Consulte o [documento limites de Luis](https://docs.microsoft.com/azure/cognitive-services/luis/luis-boundaries)
Número máximo de entidades (no total) por modelo | 100
Número máximo de ações por modelo | 32
Número máximo de caixas de diálogo de treinamento por modelo | 1000
Número máximo de folheios de usuário por caixa de diálogo de treinamento | 100
Número máximo de caixas de diálogo de log por modelo | Nenhum limite predefinido, mas as caixas de diálogo de log só são retidas por um período fixo antes de serem descartadas.  Além disso, a interface do usuário do Conversation Learner mostrará 100 caixas de diálogo de log por vez. 
Número máximo de modelos por usuário | Nenhum limite predefinido
Número máximo de ações sequenciais não esperadas | 5 (*)

(*) Após 5 ações sequenciais não esperadas, todas as ações de não espera são mascaradas e Conversation Learner escolherá entre as ações de espera disponíveis.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Introdução ao Conversation Learner](./quickstart.md)
