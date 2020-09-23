---
title: Boost Azure geriu desempenho do disco
description: Saiba mais sobre os níveis de desempenho para discos geridos, bem como como atualizar os níveis de desempenho para os seus discos geridos.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/22/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 4ad0ce1aef45dc4126d3ce17c7093b885f1b8bd7
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938017"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Níveis de desempenho para discos geridos (pré-visualização)

A Azure Disk Storage oferece atualmente capacidades de rebentamento incorporadas para obter um desempenho mais elevado para lidar com tráfego inesperado de curto prazo. Os SSDs Premium têm a flexibilidade para aumentar o desempenho do disco sem aumentar o tamanho real do disco, permitindo-lhe corresponder às necessidades de desempenho da sua carga de trabalho e reduzir custos. Isto é ideal para eventos que temporariamente requerem um nível de desempenho consistentemente mais elevado, como compras de férias, testes de desempenho ou execução de um ambiente de treino. Para lidar com estes eventos, pode selecionar um nível de desempenho mais elevado durante o tempo necessário e voltar ao nível original quando o desempenho adicional já não for necessário.

## <a name="how-it-works"></a>Como funciona

Quando implementa ou disponibiliza um disco pela primeira vez, o nível de desempenho de base para esse disco é definido com base no tamanho do disco provisionado. Um nível de desempenho mais elevado pode ser selecionado para satisfazer uma maior procura e, quando esse desempenho já não for necessário, pode voltar ao nível de desempenho inicial da linha de base. Por exemplo, se forre um disco P10 (128 GiB), o seu nível de desempenho de base é definido como P10 (500 IOPS e 100 MB/s). Pode atualizar o nível para corresponder ao desempenho de P50 (7500 IOPS e 250 MB/s) sem aumentar o tamanho do disco e regressar ao P10 quando o desempenho mais elevado já não for necessário.

| Tamanho do disco | Nível de desempenho de base | Pode ser atualizado para |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2, P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 8 GiB | P2 | P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 16 GiB | P3 | P4, P6, P10, P15, P20, P30, P40, P50 | 
| 32 GiB | P4 | P6, P10, P15, P20, P30, P40, P50 |
| 64 GiB | P6 | P10, P15, P20, P30, P40, P50 |
| 128 GiB | P10 | P15, P20, P30, P40, P50 |
| 256 GiB | P15 | P20, P30, P40, P50 |
| 512 GiB | P20 | P30, P40, P50 |
| 1 TiB | P30 | P40, P50 |
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | Nenhum |
| 8 TiB | P60 |  P70, P80 |
| 16 TiB | P70 | P80 |
| 32 TiB | P80 | Nenhum |

## <a name="restrictions"></a>Restrições

- Atualmente apenas suportado para SSDs premium.
- Os discos devem ser separados de um VM em funcionamento antes de alterar os níveis.
- A utilização dos níveis de desempenho P60, P70 e P80 está restrita aos discos de 4096 GiB ou superiores.

## <a name="regional-availability"></a>Disponibilidade regional

Atualmente, o ajustamento do nível de desempenho de um disco gerido só está atualmente disponível para SSDs premium nas seguintes regiões:

- E.U.A. Centro-Oeste 
- Leste 2 EUA 
- Europa Ocidental
- Leste da Austrália 
- Sudeste da Austrália 
- Sul da Índia

## <a name="createupdate-a-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Criar/atualizar um disco de dados com um nível superior ao nível de base

1. Crie um disco de dados vazio com um nível superior ao nível de base ou atualize o nível de um disco superior ao nível de base utilizando o modelo de amostra [CreateUpdateDataDiskWithTier.jsem](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateDataDiskWithTier.json)

     ```cli
     subscriptionId=<yourSubscriptionIDHere>
     resourceGroupName=<yourResourceGroupNameHere>
     diskName=<yourDiskNameHere>
     diskSize=<yourDiskSizeHere>
     performanceTier=<yourDesiredPerformanceTier>
     region=<yourRegionHere>
    
     az login
    
     az account set --subscription $subscriptionId
    
     az group deployment create -g $resourceGroupName \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateDataDiskWithTier.json" \
     --parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier" "dataDiskSizeInGb=$diskSize"
     ```

1. Confirme o nível do disco

    ```cli
    az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-06-30 --query [properties.tier] -o tsv
     ```

## <a name="createupdate-an-os-disk-with-a-tier-higher-than-the-baseline-tier"></a>Criar/atualizar um disco DE com um nível superior ao nível de base

1. Crie um disco DE a partir de uma imagem de mercado ou atualize o nível de um disco de OS superior ao nível de base utilizando o modelo de amostra [CreateUpdateOSDiskWithTier.jsem](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateOSDiskWithTier.json)

     ```cli
     resourceGroupName=<yourResourceGroupNameHere>
     diskName=<yourDiskNameHere>
     performanceTier=<yourDesiredPerformanceTier>
     region=<yourRegionHere>
    
     az group deployment create -g $resourceGroupName \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateOSDiskWithTier.json" \
     --parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier"
     ```
 
 1. Confirme o nível do disco
 
     ```cli
     az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-06-30 --query [properties.tier] -o tsv
     ```

## <a name="next-steps"></a>Passos seguintes

Se tiver de redimensionar um disco para tirar partido dos níveis de desempenho maiores, consulte os nossos artigos sobre o assunto:

- [Expandir discos rígidos virtuais num Linux VM com o Azure CLI](linux/expand-disks.md)
- [Expandir um disco gerido ligado a uma máquina virtual do Windows](windows/expand-os-disk.md)