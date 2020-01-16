---
title: Arquiteturas de referência de renderização do Azure – lote do Azure
description: Arquiteturas para usar o lote do Azure e outros serviços do Azure para estender um farm de renderização local intermitentemente para a nuvem
services: batch
ms.service: batch
author: davefellows
manager: gwallace
ms.author: jushiman
ms.date: 02/07/2019
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: a04f59983aca4b7db1a58ab4e8b8a2da47a52783
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76026304"
---
# <a name="reference-architectures-for-azure-rendering"></a>Arquiteturas de referência para renderização do Azure

Este artigo mostra diagramas de arquitetura de alto nível para cenários para estender ou "disparo", um farm de renderização local para o Azure. Os exemplos mostram diferentes opções para serviços de computação, rede e armazenamento do Azure.

## <a name="hybrid-with-nfs-or-cfs"></a>Híbrido com NFS ou CFS

O diagrama a seguir mostra um cenário híbrido que inclui os seguintes serviços do Azure:

* **Computação** -pool do lote do Azure ou conjunto de dimensionamento de máquinas virtuais.

* **Rede** local: Azure EXPRESSROUTE ou VPN. Azure: VNet do Azure.

* Arquivos de entrada e saída de **armazenamento** : NFS ou CFS usando VMs do Azure, sincronizado com o armazenamento local por meio de sincronização de arquivos do Azure ou rsync. Como alternativa: avere vFXT para entrada ou saída de arquivos em dispositivos NAS locais usando NFS.

  ![Intermitência de nuvem-híbrido com NFS ou CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Híbrido com Blobfuse

O diagrama a seguir mostra um cenário híbrido que inclui os seguintes serviços do Azure:

* **Computação** -pool do lote do Azure ou conjunto de dimensionamento de máquinas virtuais.

* **Rede** local: Azure EXPRESSROUTE ou VPN. Azure: VNet do Azure.

* Arquivos de entrada e saída de **armazenamento** : armazenamento de BLOBs, montados em recursos de computação por meio do Azure Blobfuse.

  ![Intermitência de nuvem-híbrido com Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Computação híbrida e armazenamento

O diagrama a seguir mostra um cenário híbrido totalmente conectado para computação e armazenamento e inclui os seguintes serviços do Azure:

* **Computação** -pool do lote do Azure ou conjunto de dimensionamento de máquinas virtuais.

* **Rede** local: Azure EXPRESSROUTE ou VPN. Azure: VNet do Azure.

* **Armazenamento** -entre instalações: avere vFXT. Arquivamento opcional de arquivos locais por meio de Azure Data Box para armazenamento de BLOBs ou FXT avere local para aceleração de NAS.

  ![Intermitência de nuvem-computação híbrida e armazenamento](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como usar [os gerenciadores de renderização](batch-rendering-render-managers.md) com o lote do Azure.

* Saiba mais sobre as opções de [renderização no Azure](batch-rendering-service.md).