---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.openlocfilehash: d03d904de68720874ea175c95244ba80c586df82
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133892"
---
## <a name="change-the-model-update-frequency"></a>Alterar a frequência de atualização do modelo

No portal Azure, no recurso Personalizer na página **Configuração,** altere a **frequência de atualização** do Modelo para 10 segundos. Esta curta duração irá treinar o serviço rapidamente, permitindo-lhe ver como a ação de topo muda para cada iteração.

![Alterar a frequência de atualização do modelo](../media/settings/configure-model-update-frequency-settings.png)

Quando um loop Personalizer é iniciado pela primeira vez, não há modelo, uma vez que não houve nenhuma chamada da Reward API para treinar. As chamadas de classificação retornarão as mesmas probabilidades para cada item. A sua aplicação deve ainda classificar sempre o conteúdo utilizando a saída do RewardActionId.