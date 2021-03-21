---
title: Unidades reservadas de mídia de escala (MRUs) CLI
description: Este tópico mostra como usar o CLI para escalar o processamento de mídia com a Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: a07c4a20b854e09daf3b320b8c99757ca99b2578
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213815"
---
# <a name="how-to-scale-media-reserved-units"></a>Como escalar unidades reservadas para os meios de comunicação

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo mostra-lhe como escalar Unidades Reservadas de Mídia (MRSs) para uma codificação mais rápida.

## <a name="prerequisites"></a>Pré-requisitos

[Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social.

Compreender [unidades reservadas de mídia.](concept-media-reserved-units.md)

## <a name="scale-media-reserved-units-with-cli"></a>Unidades reservadas para mídia de escala com CLI

Execute o comando `mru`.

O comando [mru da conta AZ AMS](/cli/azure/ams/account/mru) define As Unidades Reservadas de Mídia na conta "amsaccount" utilizando os parâmetros **de contagem** e **tipo.**

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Faturação

É cobrado com base no número de minutos que as Unidades Reservadas de Mídia estão a ser adsteadas na sua conta. Isto ocorre independentemente de existir algum Jobs a decorrer na sua conta. Para obter uma explicação detalhada, consulte a secção de PERGUNTAS Frequentes da página [de preços dos Serviços de Comunicação Social.](https://azure.microsoft.com/pricing/details/media-services/)   

## <a name="next-step"></a>Passo seguinte

[Analisar vídeos](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>Ver também

* [Quotas e limites](limits-quotas-constraints.md)
