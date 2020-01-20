---
title: Colocalizar conjuntos de dimensionamento de máquinas virtuais do Azure
description: Saiba como a colocalização de recursos do conjunto de dimensionamento de máquinas virtuais do Azure pode melhorar o desempenho.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/14/2019
ms.author: cynthn
ms.openlocfilehash: 9d6e4e15c552cc4467dd17221a1ba901fd8e4f58
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275206"
---
# <a name="co-location"></a>Colocalização

Um dos maiores colaboradores da latência entre as VMs é simplesmente a distância.

## <a name="preview-proximity-placement-groups"></a>Visualização: grupos de posicionamento de proximidade 

[!INCLUDE [virtual-machines-common-ppg-overview](../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Passos seguintes

Crie um [grupo de posicionamento de proximidade](proximity-placement-groups.md) para seu conjunto de dimensionamento.

