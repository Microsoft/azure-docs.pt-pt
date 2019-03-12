---
title: Disponibilidade de recursos do Azure Container Instances
description: Disponibilidade dos recursos de computação e memória para o serviço do Azure Container Instances em diferentes regiões do Azure.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 03/01/2019
ms.author: danlep
ms.openlocfilehash: 1ca23a95c746139963aa70ed20bb888152fd5cd8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554538"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Disponibilidade de recursos do Azure Container Instances em regiões do Azure

Este artigo fornece detalhes sobre a disponibilidade de recursos de computação e memória de instâncias de contentor do Azure em regiões do Azure. 

Os valores apresentados são os recursos máximos disponíveis por implementação de um [grupo de contentores](container-instances-container-groups.md). Os valores são atualizados no momento da publicação. Para obter informações atualizadas, utilize o [capacidades de lista](/rest/api/container-instances/listcapabilities/listcapabilities) API. 

> [!NOTE]
> Grupos de contentor criados dentro destes limites de recursos estão sujeitos a disponibilidade numa região de implementação. Quando uma região está sob muita carga, pode ocorrer uma falha ao implementar instâncias. Para mitigar essa falha de implementação, experimente implementar instâncias com definições de recursos inferior ou tente implementar mais tarde.

Para obter informações sobre as quotas e outros limites das implementações, consulte [Quotas e limites do Azure Container Instances](container-instances-quotas.md).

## <a name="availability---general"></a>Disponibilidade – geral

| Localização | SO | CPU | Memória (GB) |
| -------- | -- | :---: | :-----------: |
| Canadá Central, E.U.A. Central, E.U.A. Leste 2, Centro-Sul dos E.U.A. | Linux | 4 | 16 |
| EUA Leste, Europa do Norte, Europa Ocidental, EUA Oeste, EUA Oeste 2 | Linux | 4 | 14 |
| Este do Japão | Linux | 2 | 8 |
| Leste da Austrália, Sudeste asiático | Linux | 2 | 7 |
| Índia central, Ásia Oriental, Norte dos E.U.A., Centro-sul da Índia | Linux | 2 | 3.5 |
| EUA Leste, Europa Ocidental, EUA Oeste | Windows | 4 | 14 |
| Leste da Austrália, Canadá Central, Índia Central, E.U.A. Central, Ásia Oriental, E.U.A. Leste 2, leste do Japão, Centro-Norte, Europa do Norte, EUA Centro-Sul, Sul da Índia, Sudeste asiático, E.U.A. oeste 2 | Windows | 2 | 3.5 |

## <a name="availability---virtual-network-deployment-preview"></a>Disponibilidade - implantação de rede Virtual (pré-visualização)

As seguintes regiões e os recursos estão disponíveis para um grupo de contentores implementado numa [rede virtual do Azure](container-instances-vnet.md) (pré-visualização)

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Disponibilidade - recursos GPU (pré-visualização)

As seguintes regiões e os recursos estão disponíveis para um grupo de contentores implementado com [recursos GPU](container-instances-gpu.md) (pré-visualização)

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Passos Seguintes

Informe a equipa se gostaria de ver regiões adicionais ou disponibilidade de recursos maior ao [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Para obter informações sobre resolução de problemas de implementação de instância de contentor, consulte [resolver problemas de implementação com o Azure Container Instances](container-instances-troubleshooting.md).
