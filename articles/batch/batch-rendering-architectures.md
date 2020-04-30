---
title: Arquiteturas de referência de renderização azure
description: Arquiteturas para usar O Lote Azure e outros serviços Azure para estender uma fazenda no local, explodindo até a nuvem
ms.date: 02/07/2019
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: bbdb5eb39805ac87bf90216e5fbeedae91b423f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115776"
---
# <a name="reference-architectures-for-azure-rendering"></a>Arquiteturas de referência para renderização azure

Este artigo mostra diagramas de arquitetura de alto nível para cenários que se estendem, ou "burst", uma fazenda no local render a Azure. Os exemplos mostram diferentes opções para serviços de computação, networking e armazenamento Azure.

## <a name="hybrid-with-nfs-or-cfs"></a>Híbrido com NFS ou CFS

O diagrama seguinte mostra um cenário híbrido que inclui os seguintes serviços Azure:

* **Compute** - Piscina de Lote Azure ou Conjunto de Escala de Máquina virtual.

* **Rede** - No local: Azure ExpressRoute ou VPN. Azure: Azure VNet.

* **Armazenamento** - Ficheiros de entrada e saída: NFS ou CFS utilizando VMs Azure, sincronizados com armazenamento no local através do Azure File Sync ou RSync. Alternativamente: Avere vFXT para inserir ou passar ficheiros a partir de dispositivos NAS no local utilizando NFS.

  ![Cloud bursting - Híbrido com NFS ou CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Híbrido com Blobfuse

O diagrama seguinte mostra um cenário híbrido que inclui os seguintes serviços Azure:

* **Compute** - Piscina de Lote Azure ou Conjunto de Escala de Máquina virtual.

* **Rede** - No local: Azure ExpressRoute ou VPN. Azure: Azure VNet.

* **Armazenamento** - Ficheiros de entrada e saída: Armazenamento blob, montado para calcular recursos através de Azure Blobfuse.

  ![Cloud bursting - Híbrido com Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Computação híbrida e armazenamento

O diagrama seguinte mostra um cenário híbrido totalmente conectado tanto para a computação como para o armazenamento e inclui os seguintes serviços Azure:

* **Compute** - Piscina de Lote Azure ou Conjunto de Escala de Máquina virtual.

* **Rede** - No local: Azure ExpressRoute ou VPN. Azure: Azure VNet.

* **Armazenamento** - Cross-premises: Avere vFXT. Arquivamento opcional de ficheiros no local através da Caixa de Dados Azure para o armazenamento blob, ou no local Avere FXT para aceleração nas.

  ![Cloud bursting - Calculador híbrido e armazenamento](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a utilização de [gestores render](batch-rendering-render-managers.md) com o Lote Azure.

* Saiba mais sobre as opções para [renderização em Azure.](batch-rendering-service.md)