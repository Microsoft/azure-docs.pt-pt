---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 08/25/2020
ms.openlocfilehash: 4eacc1c4e863ad1a278a4974bb0f6c101aafe7e0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89055404"
---
### <a name="change-the-model-update-frequency"></a>Alterar a frequência de atualização do modelo

No portal Azure, no recurso Personalizer na página **Configuração,** altere a **frequência de atualização** do Modelo para 10 segundos. Esta curta duração irá treinar o serviço rapidamente, permitindo-lhe ver como a ação de topo muda para cada iteração.

![Alterar a frequência de atualização do modelo](../media/settings/configure-model-update-frequency-settings.png)

Quando um loop Personalizer é iniciado pela primeira vez, não há modelo, uma vez que não houve nenhuma chamada da Reward API para treinar. As chamadas de classificação retornarão as mesmas probabilidades para cada item. A sua aplicação deve ainda classificar sempre o conteúdo utilizando a saída do RewardActionId.