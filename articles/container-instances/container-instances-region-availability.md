---
title: Disponibilidade de recursos das instâncias de contêiner do Azure
description: Disponibilidade de recursos de computação e memória para o serviço de instâncias de contêiner do Azure em diferentes regiões do Azure.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: overview
ms.date: 05/14/2019
ms.author: danlep
ms.openlocfilehash: 14e7b9a3ea11e59aabeb901c4039e69208ea0cfd
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325708"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Disponibilidade de recursos para instâncias de contêiner do Azure em regiões do Azure

Este artigo detalha a disponibilidade de recursos de computação e memória de instâncias de contêiner do Azure em regiões do Azure. 

Os valores apresentados são o máximo de recursos disponíveis por implantação de um [grupo](container-instances-container-groups.md)de contêineres. Os valores são atuais no momento da publicação. 

> [!NOTE]
> Os grupos de contêineres criados dentro desses limites de recursos estão sujeitos à disponibilidade na região de implantação. Quando uma região está sob muita carga, pode ocorrer uma falha ao implementar instâncias. Para atenuar essa falha de implantação, tente implantar instâncias com configurações de recursos menores ou tente a implantação mais tarde.

Para obter informações sobre cotas e outros limites em suas implantações, consulte [cotas e limites para instâncias de contêiner do Azure](container-instances-quotas.md).

## <a name="availability---general"></a>Disponibilidade-geral

As seguintes regiões e recursos estão disponíveis para grupos de contêineres com Linux e contêineres com [suporte](container-instances-faq.md#what-windows-base-os-images-are-supported) do Windows Server 2016.

| Location | OS | CPU | Memória (GB) |
| -------- | -- | :---: | :-----------: |
| Canadá central, Índia central, EUA Central, Ásia Oriental, leste dos EUA, leste dos EUA 2, Europa Setentrional, Sul EUA Central, Sudeste Asiático, Sul do Reino Unido, oeste dos EUA | Linux | 4 | 16 |
| Europa Ocidental, oeste dos EUA 2 | Linux | 4 | 14 |
| Leste da Austrália, leste do Japão | Linux | 2 | 8 |
| Norte EUA Central, sul da Índia | Linux | 2 | 3.5 |
| Europa Ocidental | Windows | 4 | 16 |
| Leste dos EUA, oeste dos EUA | Windows | 4 | 14 |
| Leste da Austrália, Canadá central, Índia central, EUA Central, Ásia Oriental, leste dos EUA 2, leste do Japão, norte EUA Central, Europa Setentrional, Sul EUA Central, Sudeste Asiático, sul da Índia, Sul do Reino Unido, oeste dos EUA 2 | Windows | 2 | 3.5 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Disponibilidade-Windows Server 2019 LTSC, 1809 implantações (versão prévia)

As seguintes regiões e recursos estão disponíveis para grupos de contêineres com contêineres baseados no Windows Server 2019 (versão prévia).

| Location | OS | CPU | Memória (GB) |
| -------- | -- | :---: | :-----------: |
| Sudeste Asiático, Europa Setentrional, Europa Ocidental, EUA Central, leste dos EUA, oeste dos EUA, oeste dos EUA 2 | Windows | 4 | 16 |
| EUA Leste 2 | Windows | 2 | 3.5 |


## <a name="availability---virtual-network-deployment-preview"></a>Disponibilidade-implantação de rede virtual (versão prévia)

As seguintes regiões e recursos estão disponíveis para um grupo de contêineres implantados em uma [rede virtual do Azure](container-instances-vnet.md) (versão prévia).

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Disponibilidade-recursos de GPU (visualização)

As seguintes regiões e recursos estão disponíveis para um grupo de contêineres implantados com [recursos de GPU](container-instances-gpu.md) (versão prévia).

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Passos Seguintes

Deixe a equipe saber se você gostaria de ver regiões adicionais ou maior disponibilidade de recursos em [aka.ms/ACI/feedback](https://aka.ms/aci/feedback).

Para obter informações sobre como solucionar problemas de implantação de instância de contêiner, consulte [solucionar problemas de implantação com instâncias de contêiner do Azure](container-instances-troubleshooting.md).
