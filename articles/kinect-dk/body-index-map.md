---
title: Mapa do índice do corpo de Azure Kinect
description: Entenda como consultar um mapa de índice de rastreio corporal no Azure Kinect DK.
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, porting, corpo, tracking, índice, segmentação, mapa
ms.openlocfilehash: 8c128e59cef515aafb4f59794f0f644b90fd625d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277553"
---
# <a name="azure-kinect-body-tracking-index-map"></a>Mapa do índice de rastreio do corpo de Azure Kinect

O mapa do índice do corpo inclui o mapa de segmentação de cada corpo na captura de câmaras de profundidade. Cada pixel mapeia para o pixel correspondente na profundidade ou na imagem IR. O valor para cada pixel representa a que corpo o pixel pertence. Pode ser de fundo `K4ABT_BODY_INDEX_MAP_BACKGROUND` (valor) ou o índice de um `k4abt_body_t` detetado.

![Exemplo do mapa do índice do corpo](./media/concepts/body-index-map.png)

>[!NOTE]
> O índice do corpo é diferente do id do corpo. Pode consultar o id do corpo a partir de um determinado índice corporal, chamando API: [k4abt_frame_get_body_id()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92).


## <a name="using-body-index-map"></a>Usando mapa de índice de corpo

O mapa do índice do corpo é armazenado como um `k4a_image_t` e tem a mesma resolução que a profundidade ou a imagem de IR. Cada pixel tem um valor de 8 bits. Pode ser questionado a partir de um `k4abt_frame_t` `k4abt_frame_get_body_index_map` chamando. O desenvolvedor é responsável por libertar a memória do mapa do índice do corpo, chamando `k4a_image_release()` .

## <a name="next-steps"></a>Passos seguintes

[Criar a sua primeira aplicação de deteção de movimentos](build-first-body-app.md)
