---
title: Disponibilidade de recursos por região
description: Disponibilidade de recursos de computação e memória para o serviço Azure Container Instances em diferentes regiões do Azure.
ms.topic: article
ms.date: 04/27/2020
ms.openlocfilehash: 97baa5199a1803bd967c0b55c846908ea5a2ddcf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89565434"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Disponibilidade de recursos para instâncias de contentores Azure nas regiões de Azure

Este artigo detalha a disponibilidade de recursos de cálculo, memória e armazenamento de instâncias Azure nas regiões de Azure e pelo sistema operativo-alvo. 

Os valores apresentados são os recursos máximos disponíveis por implantação de um grupo de [contentores.](container-instances-container-groups.md) Os valores são atuais no momento da publicação. 

> [!NOTE]
> Os grupos de contentores criados dentro destes limites de recursos estão sujeitos à disponibilidade na região de implantação. Quando uma região está sob muita carga, pode ocorrer uma falha ao implementar instâncias. Para mitigar tal falha de implantação, tente implementar casos com configurações de recursos mais baixas ou tente a sua implantação posteriormente ou numa região diferente com recursos disponíveis.

Para obter informações sobre quotas e outros limites nas suas implementações, consulte [quotas e limites para instâncias de contentores Azure](container-instances-quotas.md).

## <a name="availability---general"></a>Disponibilidade - Geral

As seguintes regiões e recursos máximos estão disponíveis para grupos de contentores com Linux e contentores [suportados](container-instances-faq.md#what-windows-base-os-images-are-supported) pelo Windows Server 2016.

| Regiões | SO | CPU Máxima | Memória Máxima (GB) | Armazenamento (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| Brasil Sul, Canadá Central, Índia Central, Eua Central, Leste Asiático, Leste dos EUA, Leste dos EUA 2, Norte da Europa, Centro-Sul dos EUA, Sudeste Asiático, Sul da Índia, Reino Unido Sul, Europa Ocidental, Eua Ocidental, Oeste DOS EUA 2 | Linux | 4 | 16 | 50 |
| Austrália Leste, Japão Leste | Linux | 2 | 8 | 50 |
| E.U.A. Centro-Norte | Linux | 2 | 3.5 | 50 |
| Brasil Sul, Japão Leste, Europa Ocidental | Windows | 4 | 16 | 20 |
| Leste dos EUA, Oeste dos EUA | Windows | 4 | 14 | 20 |
| Austrália Leste, Canadá Central, Índia Central, Eua Central, Leste Ásia, Leste DOS EUA 2, Norte da Europa Central, Norte da Europa, Centro-Sul dos EUA, Sudeste Asiático, Sul da Índia, Reino Unido Sul, Oeste DOS EUA 2 | Windows | 2 | 3.5 | 20 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Disponibilidade - Windows Server 2019 LTSC, 1809 implementações (pré-visualização)

As seguintes regiões e recursos máximos estão disponíveis para grupos de contentores com contentores baseados no Windows Server 2019 (pré-visualização).

| Regiões | SO | CPU Máxima | Memória Máxima (GB) | Armazenamento (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| Austrália Leste, Brasil Sul, Canadá Central, Índia Central, Eua Central, Ásia Oriental, Leste dos EUA, Japão Leste, Norte Central DOS EUA, Norte da Europa, Sudeste Da Ásia, Sul da Índia, Reino Unido Sul, Europa Ocidental | Windows | 4 | 16 | 20 |
| Leste dos EUA 2, Oeste DOS EUA 2 | Windows | 2 | 3.5 | 20 |


## <a name="availability---virtual-network-deployment"></a>Disponibilidade - Implementação de rede virtual

As seguintes regiões e recursos máximos estão disponíveis para um grupo de contentores implantado numa [rede virtual Azure.](container-instances-vnet.md)

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Disponibilidade - Recursos da GPU (pré-visualização)

As seguintes regiões e recursos máximos estão disponíveis para um grupo de contentores implantado com [recursos gpu](container-instances-gpu.md) (pré-visualização).

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Passos seguintes

Informe a equipa se pretender ver regiões adicionais ou aumentar a disponibilidade de recursos em [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Para obter informações sobre a resolução de situações de resolução de [problemas, consulte problemas de implantação de resolução de problemas com instâncias do contentor Azure](container-instances-troubleshooting.md).


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
