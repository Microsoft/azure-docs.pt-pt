---
title: Disponibilidade de recursos por região
description: Disponibilidade de recursos de computação e memória para o serviço De casos de contentores Azure em diferentes regiões de Azure.
ms.topic: article
ms.date: 04/27/2020
ms.author: danlep
ms.openlocfilehash: 119c1202fe59e02a1b6ff802e3dbdc4d52133abe
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583895"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Disponibilidade de recursos para instâncias de contentores azure nas regiões de Azure

Este artigo detalha a disponibilidade de dados de instâncias de contentores azure, memória e armazenamento nas regiões de Azure e através do sistema operativo-alvo. 

Os valores apresentados são os recursos máximos disponíveis por implantação de um grupo de [contentores.](container-instances-container-groups.md) Os valores são atuais no momento da publicação. 

> [!NOTE]
> Os grupos de contentores criados dentro destes limites de recursos estão sujeitos à disponibilidade dentro da região de implantação. Quando uma região está sob muita carga, pode ocorrer uma falha ao implementar instâncias. Para mitigar tal falha de implementação, tente implementar instâncias com configurações de recursos mais baixas ou tente a sua implantação mais tarde ou numa região diferente com recursos disponíveis.

Para obter informações sobre quotas e outros limites nas suas implantações, consulte [Quotas e limites para instâncias de contentores Azure](container-instances-quotas.md).

## <a name="availability---general"></a>Disponibilidade - Geral

As regiões seguintes e os recursos máximos estão disponíveis para grupos de contentores com o Linux e contentores [baseados](container-instances-faq.md#what-windows-base-os-images-are-supported) no Windows Server 2016.

| Regiões | SO | CPU Máxima | Memória Max (GB) | Armazenamento (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| Brasil Sul, Canadá Central, Índia Central, Eua Central, Ásia Oriental, Leste dos EUA, Leste dos EUA 2, Norte da Europa, Centro-Sul dos EUA, Sudeste Asiático, Sul da Índia, Reino Unido Sul, Europa Ocidental, Oeste DOS EUA, OESTE DOS EUA 2 | Linux | 4 | 16 | 50 |
| Austrália Leste, Japão Leste | Linux | 2 | 8 | 50 |
| E.U.A. Centro-Norte | Linux | 2 | 3.5 | 50 |
| Brasil Sul, Japão Leste, Europa Ocidental | Windows | 4 | 16 | 20 |
| Leste dos EUA, Oeste dos EUA | Windows | 4 | 14 | 20 |
| Austrália Leste, Canadá Central, Índia Central, Eua Central, Ásia Oriental, Leste DOS 2, Norte Dos EUA, Norte da Europa, Centro-Sul dos EUA, Sudeste Asiático, Índia Do Sul, Reino Unido Sul, Oeste DOS EUA 2 | Windows | 2 | 3.5 | 20 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Disponibilidade - Windows Server 2019 LTSC, 1809 implementações (pré-visualização)

As regiões seguintes e os recursos máximos estão disponíveis para grupos de contentores com contentores baseados no Windows Server 2019 (pré-visualização).

| Regiões | SO | CPU Máxima | Memória Max (GB) | Armazenamento (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| Austrália Leste, Brasil Sul, Canadá Central, Índia Central, Eua Central, Ásia Oriental, Leste dos EUA, Japão Leste, Norte Central EUA, Norte Da Europa, Centro-Sul dos EUA, Sudeste Asiático, Sul da Índia, Reino Unido Sul, Europa Ocidental | Windows | 4 | 16 | 20 |
| Leste dos EUA 2, Oeste DOS EUA 2 | Windows | 2 | 3.5 | 20 |


## <a name="availability---virtual-network-deployment"></a>Disponibilidade - Implantação de rede virtual

As seguintes regiões e recursos máximos estão disponíveis para um grupo de contentores implantado numa [rede virtual Azure.](container-instances-vnet.md)

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Disponibilidade - Recursos GPU (pré-visualização)

As seguintes regiões e recursos máximos estão disponíveis para um grupo de contentores implantado com [recursos de GPU](container-instances-gpu.md) (pré-visualização).

> [!IMPORTANT]
> Os recursos da GPU só estão disponíveis mediante solicitação. Para solicitar o acesso aos recursos da GPU, por favor envie um pedido de [apoio azure][azure-support].

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Passos seguintes

Informe a equipa se gostaria de ver regiões adicionais ou aumento da disponibilidade de recursos em [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Para obter informações sobre a implantação de casos de contentores de resolução de problemas, consulte problemas de implantação de [Problemas com instâncias de contentores Azure](container-instances-troubleshooting.md).


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
