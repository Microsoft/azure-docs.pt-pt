---
title: Articulações de rastreio do corpo de Azure Kinect
description: Compreenda a estrutura do corpo, as articulações, as coordenadas conjuntas e a hierarquia conjunta no DK kinect de Azure.
author: qm13
ms.author: quentinm
ms.reviewer: cedmonds, abalan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, porting, corpo, tracking, articulação, hierarquia, osso, conexão
ms.openlocfilehash: 4cf6ac13a93d0674f9fa144abcc3153a2d7c3350
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277537"
---
# <a name="azure-kinect-body-tracking-joints"></a>Articulações de rastreio do corpo de Azure Kinect

O rastreio do corpo de Azure Kinect pode rastrear vários corpos humanos ao mesmo tempo. Cada corpo inclui uma identificação para a correlação temporal entre os quadros e o esqueleto cinemático. O número de corpos detetados em cada quadro pode ser adquirido utilizando `k4abt_frame_get_num_bodies()` .

## <a name="joints"></a>Articulações

Posição e orientação conjuntas são estimativas relativas ao quadro global de sensores de profundidade de referência. A posição é especificada em milímetros. A orientação é expressa como um quaternion normalizado.

## <a name="joint-coordinates"></a>Coordenadas conjuntas

A posição e orientação de cada articulação formam o seu próprio sistema de coordenadas conjuntas. Todos os sistemas de coordenadas conjuntas são sistemas de coordenadas absolutas em relação ao sistema de coordenadas 3D da câmara de profundidade.

> [!NOTE]
> As coordenadas conjuntas estão na orientação do eixo. A orientação do eixo é amplamente utilizada com avatares comerciais, motores de jogo e software de renderização. A orientação do eixo simplifica os movimentos espelhados, por exemplo, elevar os dois braços em 20 graus.

![Coordenadas conjuntas](./media/concepts/joint-coordinates.png)

## <a name="joint-hierarchy"></a>Hierarquia conjunta

Um esqueleto inclui 32 articulações com a hierarquia articular que flui do centro do corpo para as extremidades. Cada ligação (osso) liga a articulação dos pais a uma articulação infantil. A figura ilustra as localizações articulares e a ligação em relação ao corpo humano.

![Hierarquia conjunta](./media/concepts/joint-hierarchy.png)

A tabela a seguir enumera as ligações comuns padrão.

|Índice |Nome comum     | Articulação dos pais   |
|------|---------------|----------------|
| 0    |PÉLVIS         | -              |
| 1    |SPINE_NAVAL    | PÉLVIS         |
| 2    |SPINE_CHEST    | SPINE_NAVAL    |
| 3    |PESCOÇO           | SPINE_CHEST    |
| 4    |CLAVICLE_LEFT  | SPINE_CHEST    |
| 5    |SHOULDER_LEFT  | CLAVICLE_LEFT  |
| 6    |ELBOW_LEFT     | SHOULDER_LEFT  |
| 7    |WRIST_LEFT     | ELBOW_LEFT     |
| 8    |HAND_LEFT      | WRIST_LEFT     |
| 9    |HANDTIP_LEFT   | HAND_LEFT      |
| 10   |THUMB_LEFT     | WRIST_LEFT     |
| 11   |CLAVICLE_RIGHT | SPINE_CHEST    |
| 12   |SHOULDER_RIGHT | CLAVICLE_RIGHT |
| 13   |ELBOW_RIGHT    | SHOULDER_RIGHT |
| 14   |WRIST_RIGHT    | ELBOW_RIGHT    |
| 15   |HAND_RIGHT     | WRIST_RIGHT    |
| 16   |HANDTIP_RIGHT  | HAND_RIGHT     |
| 17   |THUMB_RIGHT    | WRIST_RIGHT    |
| 18   |HIP_LEFT       | PÉLVIS         |
| 19   |KNEE_LEFT      | HIP_LEFT       |
| 20   |ANKLE_LEFT     | KNEE_LEFT      |
| 21   |FOOT_LEFT      | ANKLE_LEFT     |
| 22   |HIP_RIGHT      | PÉLVIS         |
| 23   |KNEE_RIGHT     | HIP_RIGHT      |
| 24   |ANKLE_RIGHT    | KNEE_RIGHT     |
| 25   |FOOT_RIGHT     | ANKLE_RIGHT    |
| 26   |HEAD           | PESCOÇO           |
| 27   |NARIZ           | HEAD           |
| 28   |EYE_LEFT       | HEAD           |
| 29   |EAR_LEFT       | HEAD           |
| 30   |EYE_RIGHT      | HEAD           |
| 31   |EAR_RIGHT      | HEAD           |

## <a name="next-steps"></a>Passos seguintes

[Mapa do índice de deteção de movimentos](body-index-map.md)
