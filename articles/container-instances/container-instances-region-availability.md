---
title: Disponibilidade de recursos do Azure Container Instances
description: Disponibilidade dos recursos de computação e memória para o serviço do Azure Container Instances em diferentes regiões do Azure.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 05/14/2019
ms.author: danlep
ms.openlocfilehash: 64b60178413e470cc7fe9b3991c6fc29b5a0f860
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794303"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Disponibilidade de recursos do Azure Container Instances em regiões do Azure

Este artigo fornece detalhes sobre a disponibilidade de recursos de computação e memória de instâncias de contentor do Azure em regiões do Azure. 

Os valores apresentados são os recursos máximos disponíveis por implementação de um [grupo de contentores](container-instances-container-groups.md). Os valores são atualizados no momento da publicação. 

> [!NOTE]
> Grupos de contentor criados dentro destes limites de recursos estão sujeitos a disponibilidade numa região de implementação. Quando uma região está sob muita carga, pode ocorrer uma falha ao implementar instâncias. Para mitigar essa falha de implementação, experimente implementar instâncias com definições de recursos inferior ou tente implementar mais tarde.

Para obter informações sobre as quotas e outros limites das implementações, consulte [Quotas e limites do Azure Container Instances](container-instances-quotas.md).

## <a name="availability---general"></a>Disponibilidade – geral

Os seguintes recursos e regiões estão disponíveis para os grupos de contentores com o Linux e [suportado](container-instances-faq.md#what-windows-base-os-images-are-supported) contêineres baseados no Windows Server 2016.

| Location | SO | CPU | Memória (GB) |
| -------- | -- | :---: | :-----------: |
| Centro do Canadá, Índia Central, E.U.A. Central, Ásia Oriental, E.U.A. leste, E.U.A. Leste 2, Europa do Norte, EUA Centro-Sul, Sudeste asiático, do Reino Unido Sul, E.U.A. oeste | Linux | 4 | 16 |
| Europa Ocidental, E.U.A. oeste 2 | Linux | 4 | 14 |
| Leste da Austrália, leste do Japão | Linux | 2 | 8 |
| Norte dos E.U.A., Centro-sul da Índia | Linux | 2 | 3.5 |
| Europa Ocidental | Windows | 4 | 16 |
| E.U.A. leste, E.U.A. oeste | Windows | 4 | 14 |
| Leste da Austrália, Canadá Central, Índia Central, E.U.A. Central, Ásia Oriental, E.U.A. Leste 2, leste do Japão, Norte dos E.U.A., Europa do Norte, E.U.A. Central Centro-Sul, Sudeste asiático, Sul da Índia, sul do Reino Unido, E.U.A. oeste 2 | Windows | 2 | 3.5 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Implementações de 1809 disponibilidade - Windows Server 2019 LTSC, (pré-visualização)

As regiões e os recursos seguintes estão disponíveis para os grupos de contentores com contentores baseados no Windows Server 2019 (pré-visualização).

| Location | SO | CPU | Memória (GB) |
| -------- | -- | :---: | :-----------: |
| Sudeste asiático, Europa do Norte, Europa Ocidental, E.U.A. Central, E.U.A. leste, E.U.A. oeste, E.U.A. oeste 2 | Windows | 4 | 16 |
| EUA Leste 2 | Windows | 2 | 3.5 |


## <a name="availability---virtual-network-deployment-preview"></a>Disponibilidade - implantação de rede Virtual (pré-visualização)

As seguintes regiões e os recursos estão disponíveis para um grupo de contentores implementado numa [rede virtual do Azure](container-instances-vnet.md) (pré-visualização).

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Disponibilidade - recursos GPU (pré-visualização)

As seguintes regiões e os recursos estão disponíveis para um grupo de contentores implementado com [recursos GPU](container-instances-gpu.md) (pré-visualização).

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Passos Seguintes

Informe a equipa se gostaria de ver regiões adicionais ou disponibilidade de recursos maior ao [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Para obter informações sobre resolução de problemas de implementação de instância de contentor, consulte [resolver problemas de implementação com o Azure Container Instances](container-instances-troubleshooting.md).
