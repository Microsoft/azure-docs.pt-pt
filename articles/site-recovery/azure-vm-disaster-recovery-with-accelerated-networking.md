---
title: Habilitar rede acelerada para recuperação de desastre de VM do Azure com Azure Site Recovery
description: Descreve como habilitar a rede acelerada com o Azure Site Recovery para a recuperação de desastre da máquina virtual do Azure
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 27691d8fab3e7c8ccd60351dc0be83898ff984ed
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622437"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>Rede acelerada com a recuperação de desastre da máquina virtual do Azure

A rede acelerada habilita o SR-IOV (virtualização de e/s de raiz única) para uma VM, melhorando muito seu desempenho de rede. Esse caminho de alto desempenho ignora o host do caminho de DataPath, reduzindo a latência, a tremulação e a utilização da CPU para uso com as cargas de trabalho de rede mais exigentes em tipos de VM com suporte. A figura a seguir mostra a comunicação entre duas VMs com e sem rede acelerada:

![Comparação](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

O Azure Site Recovery permite que você utilize os benefícios da rede acelerada para as máquinas virtuais do Azure que passarem por failover para uma região diferente do Azure. Este artigo descreve como você pode habilitar a rede acelerada para máquinas virtuais do Azure replicadas com o Azure Site Recovery.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você entendeu:
-   [Arquitetura de replicação](azure-to-azure-architecture.md) de máquina virtual do Azure
-   [Configurando a replicação](azure-to-azure-tutorial-enable-replication.md) para máquinas virtuais do Azure
-   [](azure-to-azure-tutorial-failover-failback.md) Fazendo failover Máquinas virtuais do Azure

## <a name="accelerated-networking-with-windows-vms"></a>Rede acelerada com VMs do Windows

Azure Site Recovery dá suporte à habilitação da rede acelerada para máquinas virtuais replicadas somente se a máquina virtual de origem tiver a rede acelerada habilitada. Se sua máquina virtual de origem não tiver a rede acelerada habilitada, você poderá aprender como habilitar a rede acelerada para máquinas virtuais do Windows [aqui](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Sistemas operativos suportados
As distribuições a seguir têm suporte pronto para uso na galeria do Azure:
* **Windows Server 2016 datacenter**
* **Datacenter do Windows Server 2012 R2**

### <a name="supported-vm-instances"></a>Instâncias de VM com suporte
A rede acelerada tem suporte na maioria dos tamanhos de instância de uso geral e otimizado para computação com 2 ou mais vCPUs.  Essas séries com suporte são: D/DSv2 e F/FS

Em instâncias que dão suporte a hyperthreading, a rede acelerada tem suporte em instâncias de VM com 4 ou mais vCPUs. As séries com suporte são: D/DSv3, E/ESv3, Fsv2 e MS/MMS

Para obter mais informações sobre instâncias de VM, consulte [tamanhos de VM do Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="accelerated-networking-with-linux-vms"></a>Rede acelerada com VMs Linux

Azure Site Recovery dá suporte à habilitação da rede acelerada para máquinas virtuais replicadas somente se a máquina virtual de origem tiver a rede acelerada habilitada. Se sua máquina virtual de origem não tiver a rede acelerada habilitada, você poderá aprender a habilitar a rede acelerada para máquinas virtuais Linux [aqui](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Sistemas operativos suportados
As distribuições a seguir têm suporte pronto para uso na galeria do Azure:
* **Ubuntu 16, 4**
* **SLES 12 SP3**
* **RHEL 7,4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian "Stretch" com kernel de backports**
* **Oracle Linux 7,4**

### <a name="supported-vm-instances"></a>Instâncias de VM com suporte
A rede acelerada tem suporte na maioria dos tamanhos de instância de uso geral e otimizado para computação com 2 ou mais vCPUs.  Essas séries com suporte são: D/DSv2 e F/FS

Em instâncias que dão suporte a hyperthreading, a rede acelerada tem suporte em instâncias de VM com 4 ou mais vCPUs. As séries com suporte são: D/DSv3, E/ESv3, Fsv2 e MS/MMS.

Para obter mais informações sobre instâncias de VM, consulte [tamanhos de VM Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>Habilitando a rede acelerada para VMs replicadas

Quando você [habilita a replicação](azure-to-azure-tutorial-enable-replication.md) para máquinas virtuais do Azure, site Recovery detectará automaticamente se as interfaces de rede da máquina virtual têm a rede acelerada habilitada. Se a rede acelerada já estiver habilitada, Site Recovery irá configurar automaticamente a rede acelerada nas interfaces de rede da máquina virtual replicada.

O status de rede acelerada pode ser verificado na seção **interfaces de rede** das configurações de **computação e rede** da máquina virtual replicada.

![Configuração de rede acelerada](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

Se você tiver habilitado a rede acelerada na máquina virtual de origem depois de habilitar a replicação, poderá habilitar a rede acelerada para as interfaces de rede da máquina virtual replicada pelo seguinte processo:
1. Abrir as configurações de **computação e rede** para a máquina virtual replicada
2. Clique no nome da interface de rede na seção **interfaces de rede**
3. Selecione **habilitado** na lista suspensa para rede acelerada na coluna de **destino**

![Habilitar rede acelerada](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

O processo acima também deve ser seguido para máquinas virtuais replicadas existentes, que anteriormente não tinham a rede acelerada habilitada automaticamente pelo Site Recovery.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre os [benefícios da rede acelerada](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits).
- Saiba mais sobre limitações e restrições de rede acelerada para [máquinas virtuais do Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints) e [máquinas virtuais do Linux](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints).
- Saiba mais sobre os [planos de recuperação](site-recovery-create-recovery-plans.md) para automatizar o failover de aplicativos.
