---
title: Disponibilidade de recursos por região
description: Disponibilidade de recursos de computação e memória para o serviço Azure Container Instances em diferentes regiões do Azure.
ms.topic: article
ms.date: 04/27/2020
ms.custom: references_regions
ms.openlocfilehash: b1783204ae416412579e63654ff236fbc6cf1a18
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046016"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Disponibilidade de recursos para instâncias de contentores Azure nas regiões de Azure

Este artigo detalha a disponibilidade de recursos de cálculo, memória e armazenamento de instâncias Azure nas regiões de Azure e pelo sistema operativo-alvo. Para obter uma lista geral das regiões disponíveis para instâncias de contentores de Azure, consulte [as regiões disponíveis.](https://azure.microsoft.com/regions/services/)

Os valores apresentados são os recursos máximos disponíveis por implantação de um grupo de [contentores.](container-instances-container-groups.md) Os valores são atuais no momento da publicação.

> [!NOTE]
> Os grupos de contentores criados dentro destes limites de recursos estão sujeitos à disponibilidade na região de implantação. Quando uma região está sob muita carga, pode ocorrer uma falha ao implementar instâncias. Para mitigar tal falha de implantação, tente implementar casos com configurações de recursos mais baixas ou tente a sua implantação posteriormente ou numa região diferente com recursos disponíveis.

Para obter informações sobre quotas e outros limites nas suas implementações, consulte [quotas e limites para instâncias de contentores Azure](container-instances-quotas.md).

## <a name="linux-container-groups"></a>Grupos de contentores Linux

As seguintes regiões e recursos máximos estão disponíveis para grupos de contentores com contentores Linux em implantações gerais, implantações [de redes virtuais Azure](container-instances-vnet.md) e implantações com [recursos gpu](container-instances-gpu.md) (pré-visualização).

> [!IMPORTANT]
> Os recursos máximos numa região são diferentes dependendo da sua implantação. Por exemplo, uma região pode ter um tamanho máximo de CPU e memória diferente numa implementação de rede virtual Azure do que para uma implantação geral. Essa mesma região pode também ter um conjunto diferente de valores máximos para uma implantação com recursos de GPU. Verifique o seu tipo de implantação antes de verificar as tabelas abaixo para obter os valores máximos na sua região.

| Region | CPU Máxima | Memória Máxima (GB) | VNET Max CPU | Memória Máxima VNET (GB) | Armazenamento (GB) | GPU SKUs (pré-visualização) |
| -------- | :---: | :---: | :----: | :-----: | :-------: | :----: |
| Leste da Austrália | 4 | 16 | 4 | 16 | 50 | N/D |
| Sul do Brasil | 4 | 16 | 2 | 8 | 50 | N/D |
| Canadá Central | 4 | 16 | 4 | 16 | 50 | N/D |
| Índia Central | 4 | 16 | 4 | 4 | 50 | V100 |
| E.U.A. Central | 4 | 16 | 4 | 16 | 50 | N/D |
| Ásia Leste | 4 | 16 | 4 | 16 | 50 | N/D |
| E.U.A. Leste | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| E.U.A. Leste 2 | 4 | 16 | 4 | 16 | 50 | N/D |
| França Central | 4 | 16 | 4 | 16 | 50 | N/D |
| Alemanha Centro-Oeste | 3 | 16 | N/D | N/D | 50 | N/D |
| Leste do Japão | 2 | 8 | 4 | 16 | 50 | N/D |
| Coreia do Sul Central | 4 | 16 | N/D | N/D | 50 | N/D |
| E.U.A. Centro-Norte | 2 | 3.5 | 4 | 16 | 50 | K80, P100, V100 |
| Europa do Norte | 4 | 16 | 4 | 16 | 50 | K80 |
| E.U.A. Centro-Sul | 4 | 16 | 4 | 16 | 50 | N/D |
| Sudeste Asiático | 4 | 16 | 4 | 16 | 50 | P100, V100 |
| Sul da Índia | 4 | 16 | N/D | N/D | 50 | N/D |
| Suíça Norte | 3 | 16 | N/D | N/D | 50 | N/D |
| Sul do Reino Unido | 4 | 16 | 4 | 16 | 50 | N/D |
| Uae Norte | 3 | 16 | N/D | N/D | 50 | N/D |
| E.U.A. Centro-Oeste| 4 | 16 | 4 | 16 | 50 | N/D |
| Europa Ocidental | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| E.U.A. Oeste | 4 | 16 | 4 | 16 | 50 | N/D |
| E.U.A. Oeste 2 | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |

Estão disponíveis os seguintes recursos máximos para um grupo de contentores implantado com [recursos gpu](container-instances-gpu.md) (pré-visualização).

> [!IMPORTANT]
> Neste momento, as implantações com recursos gpu não são suportadas numa implantação de rede virtual Azure e só estão disponíveis em grupos de contentores Linux.

| GPU SKUs | Contagem de GPU | CPU Máxima | Memória Máxima (GB) | Armazenamento (GB) |
| --- | --- | --- | --- | --- |
| K80 | 1 | 6 | 56 | 50 |
| K80 | 2 | 12 | 112 | 50 |
| K80 | 4 | 24 | 224 | 50 |
| P100, V100 | 1 | 6 | 112 | 50 |
| P100, V100 | 2 | 12 | 224 | 50 |
| P100, V100 | 4 | 24 | 448 | 50 |

## <a name="windows-container-groups"></a>Grupos de contentores windows

As seguintes regiões e recursos máximos estão disponíveis para grupos de contentores com recipientes [suportados e de pré-visualização](container-instances-faq.md#what-windows-base-os-images-are-supported) do Windows Server.

> [!IMPORTANT]
> Neste momento, as implementações com grupos de contentores Windows não são suportadas numa implementação de rede virtual Azure.

###  <a name="windows-server-2016"></a>Windows Server 2016

> [!NOTE]
> Consulte [a compatibilidade da versão host e do recipiente](/virtualization/windowscontainers/deploy-containers/update-containers#host-and-container-version-compatibility) para obter mais informações sobre os anfitriões 1B, 2B e 3B.

| Region | 1B/2B Max CPU | 1B/2B Memória Máxima (GB) |3B Max CPU | 3B Memória Máxima (GB) | Armazenamento (GB) |
| -------- | :---: | :---: | :----: | :-----: | :-------: |
| Leste da Austrália | 2 | 8 | 2 | 8 | 20 |
| Sul do Brasil | 4 | 16 | 4 | 16 | 20 |
| Canadá Central | 2 | 8 | 2 | 3.5 | 20 |
| Índia Central | 2 | 3.5 | 2 | 3.5 | 20 |
| E.U.A. Central | 2 | 8 | 2 | 3.5 | 20 |
| Ásia Leste | 2 | 3.5 | 2 | 3.5 | 20 |
| E.U.A. Leste | 2 | 8 | 2 | 8 | 20 |
| E.U.A. Leste 2 | 2 | 8 | 4 | 16 | 20 |
| Leste do Japão | 4 | 16 | 4 | 16 | 20 |
| Coreia do Sul Central | 4 | 16 | 4 | 16 | 20 |
| E.U.A. Centro-Norte | 2 | 8 | 4 | 16 | 20 |
| Europa do Norte | 2 | 8 | 2 | 8 | 20 |
| E.U.A. Centro-Sul | 2 | 8 | 2 | 8 | 20 |
| Sudeste Asiático | N/D | N/D | 2 | 3.5 | 20 |
| Sul da Índia | 2 | 3.5 | 2 | 3.5 | 20 |
| Sul do Reino Unido | 2 | 8 | 2 | 3.5 | 20 |
| E.U.A. Centro-Oeste | 2 | 8 | 2 | 8 | 20 |
| Europa Ocidental | 4 | 16 | 4 | 16 | 20 |
| E.U.A. Oeste | 2 | 8 | 2 | 8 | 20 |
| E.U.A. Oeste 2 | 2 | 8 | 2 | 3.5 | 20 |


### <a name="windows-server-2019-ltsc"></a>Windows Server 2019 LTSC

> [!NOTE]
> Consulte [a compatibilidade da versão host e do recipiente](/virtualization/windowscontainers/deploy-containers/update-containers#host-and-container-version-compatibility) para obter mais informações sobre os anfitriões 1B, 2B e 3B.

| Region | 1B/2B Max CPU | 1B/2B Memória Máxima (GB) |3B Max CPU | 3B Memória Máxima (GB) | Armazenamento (GB) |
| -------- | :---: | :---: | :----: | :-----: | :-------: |
| Leste da Austrália | 4 | 16 | 4 | 16 | 20 |
| Sul do Brasil | 4 | 16 | 4 | 16 | 20 |
| Canadá Central | 4 | 16 | 4 | 16 | 20 |
| Índia Central | 4 | 16 | 4 | 16 | 20 |
| E.U.A. Central | 4 | 16 | 4 | 16 | 20 |
| Ásia Leste | 4 | 16 | 4 | 16 | 20 |
| E.U.A. Leste | 4 | 16 | 4 | 16 | 20 |
| E.U.A. Leste 2 | 2 | 3.5 | 2 | 3.5 | 20 |
| França Central | 4 | 16 | 4 | 16 | 20 |
| Leste do Japão | N/D | N/D | 4 | 16 | 20 |
| Coreia do Sul Central | 4 | 16 | 4 | 16 | 20 |
| E.U.A. Centro-Norte | 4 | 16 | 4 | 16 | 20 |
| Europa do Norte | 4 | 16 | 4 | 16 | 20 |
| E.U.A. Centro-Sul | 4 | 16 | 4 | 16 | 20 |
| Sudeste Asiático | 4 | 16 | 4 | 16 | 20 |
| Sul da Índia | 4 | 16 | 4 | 16 | 20 |
| Sul do Reino Unido | 4 | 16 | 4 | 16 | 20 |
| E.U.A. Centro-Oeste | 4 | 16 | 4 | 16 | 20 |
| Europa Ocidental | 4 | 16 | 4 | 16 | 20 |
| E.U.A. Oeste | 4 | 16 | 4 | 16 | 20 |
| E.U.A. Oeste 2 | 2 | 8 | 4 | 16 | 20 |

## <a name="next-steps"></a>Passos seguintes

Informe a equipa se pretender ver regiões adicionais ou aumentar a disponibilidade de recursos em [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Para obter informações sobre a resolução de situações de resolução de [problemas, consulte problemas de implantação de resolução de problemas com instâncias do contentor Azure](container-instances-troubleshooting.md).


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
