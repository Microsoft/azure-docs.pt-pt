---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 7ef219e6b5f7547029612ec3898efec51abd4712
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122895"
---
## <a name="change-the-model-update-frequency"></a>Alterar a frequência de atualização do modelo

No portal Azure, no recurso Personalizer na página **de Configuração,** altere a frequência de **atualização** do Modelo para 10 segundos. Esta curta duração irá treinar o serviço rapidamente, permitindo-lhe ver como a ação de topo muda para cada iteração.

![Alterar a frequência de atualização do modelo](../media/settings/configure-model-update-frequency-settings.png)

Quando um loop Personalizer é inicialmente instantâneo, não há modelo, uma vez que não houve chamadas Reward API para treinar. As chamadas de classificação devolverão probabilidades iguais para cada item. A sua aplicação deve ainda classificar sempre o conteúdo utilizando a saída do RewardActionId.