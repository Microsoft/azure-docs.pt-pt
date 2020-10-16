---
title: Alterar o desempenho dos discos geridos Azure
description: Aprenda sobre os níveis de desempenho para discos geridos e aprenda a mudar os níveis de desempenho para discos geridos existentes.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 6b7d670f5e38968fa6592e323ede075abf687435
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91939022"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Níveis de desempenho para discos geridos (pré-visualização)

A Azure Disk Storage oferece atualmente capacidades de rebentamento incorporadas para proporcionar um maior desempenho para lidar com tráfego inesperado de curto prazo. Os SSDs premium têm a flexibilidade para aumentar o desempenho do disco sem aumentar o tamanho real do disco. Esta capacidade permite-lhe corresponder às suas necessidades de desempenho da carga de trabalho e reduzir custos. 

> [!NOTE]
> Esta funcionalidade encontra-se em pré-visualização. 

Esta funcionalidade é ideal para eventos que temporariamente requerem um nível de desempenho consistentemente mais elevado, como compras de férias, testes de desempenho ou execução de um ambiente de treino. Para lidar com estes eventos, pode utilizar um nível de desempenho mais elevado durante o tempo que precisar. Em seguida, pode voltar ao nível original quando já não necessitar do desempenho adicional.

## <a name="how-it-works"></a>Como funciona

Quando implementa ou disponibiliza um disco pela primeira vez, o nível de desempenho de base para esse disco é definido com base no tamanho do disco provisionado. Você pode usar um nível de desempenho mais alto para satisfazer uma maior procura. Quando já não precisares desse nível de desempenho, podes voltar ao nível de desempenho inicial da linha de base.

A sua faturação muda à medida que o seu nível muda. Por exemplo, se forre um disco P10 (128 GiB), o seu nível de desempenho de base é definido como P10 (500 IOPS e 100 MBps). Será cobrado à taxa P10. Pode atualizar o nível para corresponder ao desempenho de P50 (7.500 IOPS e 250 MBps) sem aumentar o tamanho do disco. Durante o upgrade, será cobrado à taxa P50. Quando já não precisares de um desempenho superior, podes voltar ao nível P10. O disco será novamente cobrado à taxa P10.

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

Para obter informações sobre faturação, consulte [preços de disco geridos](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="restrictions"></a>Restrições

- Atualmente esta funcionalidade é suportada apenas para SSDs premium.
- Tem de separar o disco de um VM em funcionamento antes de poder alterar o nível do disco.
- A utilização dos níveis de desempenho P60, P70 e P80 está restrita a discos de 4.096 GiB ou superiores.
- O nível de desempenho de um disco só pode ser alterado uma vez a cada 24 horas.

## <a name="regional-availability"></a>Disponibilidade regional

A capacidade de ajustar o nível de desempenho de um disco gerido está atualmente disponível apenas em SSDs premium nos EUA 2, South Central US, West Central US, Austrália Regiões do Sudeste da Austrália.

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Criar um disco de dados vazio com um nível superior ao nível de base

```azurecli
subscriptionId=<yourSubscriptionIDHere>
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
diskSize=<yourDiskSizeHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus

az login

az account set --subscription $subscriptionId

az disk create -n $diskName -g $resourceGroupName -l $region --sku Premium_LRS --size-gb $diskSize --tier $performanceTier
```
## <a name="create-an-os-disk-with-a-tier-higher-than-the-baseline-tier-from-an-azure-marketplace-image"></a>Crie um disco DE com um nível superior ao nível de base a partir de uma imagem do Azure Marketplace

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus
image=Canonical:UbuntuServer:18.04-LTS:18.04.202002180

az disk create -n $diskName -g $resourceGroupName -l $region --image-reference $image --sku Premium_LRS --tier $performanceTier
```
     
## <a name="update-the-tier-of-a-disk"></a>Atualizar o nível de um disco

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```
## <a name="show-the-tier-of-a-disk"></a>Mostrar o nível de um disco

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

## <a name="next-steps"></a>Passos seguintes

Se precisar de redimensionar um disco para tirar partido dos níveis de desempenho mais elevados, consulte estes artigos:

- [Expandir discos rígidos virtuais num Linux VM com o Azure CLI](linux/expand-disks.md)
- [Expandir um disco gerido ligado a uma máquina virtual do Windows](windows/expand-os-disk.md)
