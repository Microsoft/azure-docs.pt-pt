---
title: Azure renderizando arquiteturas de referência
description: Arquiteturas para usar Azure Batch e outros serviços Azure para estender uma fazenda no local, rebentando até a nuvem
ms.date: 02/07/2019
ms.topic: how-to
ms.custom: seodec18
ms.openlocfilehash: 784fd89c3dea88e25a2058713897c7a655c8a3af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83726524"
---
# <a name="reference-architectures-for-azure-rendering"></a>Arquiteturas de referência para renderização Azure

Este artigo mostra diagramas de arquitetura de alto nível para cenários para estender, ou "rebentar", uma fazenda no local para Azure. Os exemplos mostram diferentes opções para os serviços de computação, networking e armazenamento do Azure.

## <a name="hybrid-with-nfs-or-cfs"></a>Híbrido com NFS ou CFS

O diagrama a seguir mostra um cenário híbrido que inclui os seguintes serviços Azure:

* **Compute** - Piscina Azure Batch ou Conjunto de Balanças de Máquinas Virtuais.

* **Rede** - No local: Azure ExpressRoute ou VPN. Azure: Azure VNet.

* **Armazenamento** - Ficheiros de entrada e saída: NFS ou CFS utilizando VMs Azure, sincronizados com armazenamento no local via Azure File Sync ou RSync. Alternativamente: Avere vFXT para inserir ou desapaalar ficheiros de dispositivos NAS no local utilizando NFS.

  ![Rebentamento de nuvem - Híbrido com NFS ou CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Híbrido com Blobfuse

O diagrama a seguir mostra um cenário híbrido que inclui os seguintes serviços Azure:

* **Compute** - Piscina Azure Batch ou Conjunto de Balanças de Máquinas Virtuais.

* **Rede** - No local: Azure ExpressRoute ou VPN. Azure: Azure VNet.

* **Armazenamento** - Ficheiros de entrada e saída: Armazenamento blob, montado para calcular recursos via Azure Blobfuse.

  ![Cloud bursting - Híbrido com Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Computação híbrida e armazenamento

O diagrama a seguir mostra um cenário híbrido totalmente ligado tanto para o cálculo como para o armazenamento e inclui os seguintes serviços Azure:

* **Compute** - Piscina Azure Batch ou Conjunto de Balanças de Máquinas Virtuais.

* **Rede** - No local: Azure ExpressRoute ou VPN. Azure: Azure VNet.

* **Armazenamento** - Cross-premis: Avere vFXT. Arquivo opcional de ficheiros no local através da Caixa de Dados Azure para o armazenamento blob, ou no local Avere FXT para aceleração do NAS.

  ![Rebentamento de nuvem - Computação híbrida e armazenamento](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a utilização [de gestores de renderização](batch-rendering-render-managers.md) com a Azure Batch.

* Saiba mais sobre as opções de [renderização em Azure.](batch-rendering-service.md)