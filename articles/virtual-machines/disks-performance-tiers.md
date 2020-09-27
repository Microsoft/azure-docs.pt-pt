---
title: Alterar o desempenho dos discos geridos Azure
description: Saiba mais sobre os níveis de desempenho para discos geridos, bem como como alterar os níveis de desempenho para discos geridos existentes.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 7da500c3f18b7bf7057b0c5875bc9b39136a6483
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396591"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Níveis de desempenho para discos geridos (pré-visualização)

A Azure Disk Storage oferece atualmente capacidades de rebentamento incorporadas para obter um desempenho mais elevado para lidar com tráfego inesperado de curto prazo. Os SSDs Premium têm a flexibilidade para aumentar o desempenho do disco sem aumentar o tamanho real do disco, permitindo-lhe corresponder às necessidades de desempenho da sua carga de trabalho e reduzir custos, esta funcionalidade está atualmente em pré-visualização. Isto é ideal para eventos que temporariamente requerem um nível de desempenho consistentemente mais elevado, como compras de férias, testes de desempenho ou execução de um ambiente de treino. Para lidar com estes eventos, pode selecionar um nível de desempenho mais elevado durante o tempo necessário e voltar ao nível original quando o desempenho adicional já não for necessário.

## <a name="how-it-works"></a>Como funciona

Quando implementa ou disponibiliza um disco pela primeira vez, o nível de desempenho de base para esse disco é definido com base no tamanho do disco provisionado. Um nível de desempenho mais elevado pode ser selecionado para satisfazer uma maior procura e, quando esse desempenho já não for necessário, pode voltar ao nível de desempenho inicial da linha de base.

A sua faturação muda à medida que o seu nível muda. Por exemplo, se forre um disco P10 (128 GiB), o seu nível de desempenho de base é definido como P10 (500 IOPS e 100 MB/s) e será faturado à taxa P10. Pode atualizar o nível para corresponder ao desempenho de P50 (7500 IOPS e 250 MB/s) sem aumentar o tamanho do disco, período durante o qual será faturado à taxa P50. Quando o desempenho mais elevado já não for necessário, pode voltar ao nível P10 e o disco será novamente faturado à taxa P10.

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

- Atualmente apenas suportado para SSDs premium.
- Os discos devem ser separados de um VM em funcionamento antes de alterar os níveis.
- A utilização dos níveis de desempenho P60, P70 e P80 está restrita aos discos de 4096 GiB ou superiores.
- Um nível de desempenho dos discos só pode ser alterado uma vez a cada 24 horas.

## <a name="regional-availability"></a>Disponibilidade regional

Atualmente, o ajustamento do nível de desempenho de um disco gerido só está atualmente disponível para SSDs premium nas seguintes regiões:

- E.U.A. Centro-Oeste 

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

Se tiver de redimensionar um disco para tirar partido dos níveis de desempenho maiores, consulte os nossos artigos sobre o assunto:

- [Expandir discos rígidos virtuais num Linux VM com o Azure CLI](linux/expand-disks.md)
- [Expandir um disco gerido ligado a uma máquina virtual do Windows](windows/expand-os-disk.md)
