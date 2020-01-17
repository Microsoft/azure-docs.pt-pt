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
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122895"
---
## <a name="change-the-model-update-frequency"></a>Alterar a frequência de atualização do modelo

No portal do Azure, no recurso personalizado na página **configuração** , altere a frequência de atualização do **modelo** para 10 segundos. Essa curta duração treinará o serviço rapidamente, permitindo que você veja como as principais ações são alteradas para cada iteração.

![Alterar a frequência de atualização do modelo](../media/settings/configure-model-update-frequency-settings.png)

Quando um loop do personalizador é instanciado pela primeira vez, não há nenhum modelo, pois não há chamadas de API de recompensa para treinar. Chamadas de classificação retornarão probabilidades iguais para cada item. Seu aplicativo ainda deve sempre classificar o conteúdo usando a saída de RewardActionId.