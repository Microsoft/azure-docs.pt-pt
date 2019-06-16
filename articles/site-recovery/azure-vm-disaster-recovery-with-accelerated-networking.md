---
title: Accelerated Networking com a recuperação após desastre de máquina virtual do Azure | Documentos da Microsoft
description: Descreve como ativar o Accelerated Networking com o Azure Site Recovery para recuperação após desastre de máquina virtual do Azure
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: c7edc7979636ced8697aa5ad724f9c6600d840bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60772524"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>Redes aceleradas com a recuperação após desastre de máquina virtual do Azure

Funcionamento em rede acelerado que permite que a virtualização de e/s de raiz única (SR-IOV) para uma VM, melhorando em grande medida o desempenho de rede. Este caminho de alto desempenho ignora o anfitrião a partir de datapath, reduzindo a latência, distorção e a utilização da CPU, para utilização com as mais exigentes cargas de trabalho de rede sobre os tipos VM suportados. A imagem seguinte mostra a comunicação entre duas VMs com e sem redes aceleradas:

![Comparação](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

O Azure Site Recovery permite-lhe utilizar os benefícios de Accelerated Networking para máquinas virtuais do Azure com ativação pós-falha para uma região do Azure diferente. Este artigo descreve como pode ativar o Accelerated Networking para máquinas virtuais do Azure replicadas com o Azure Site Recovery.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que compreende:
-   Máquina virtual do Azure [arquitetura da replicação](azure-to-azure-architecture.md)
-   [Configurar a replicação](azure-to-azure-tutorial-enable-replication.md) para máquinas virtuais do Azure
-   [Efetuar a ativação pós-falha](azure-to-azure-tutorial-failover-failback.md) máquinas virtuais do Azure

## <a name="accelerated-networking-with-windows-vms"></a>Redes aceleradas com VMs do Windows

O Azure Site Recovery suporta ativar redes aceleradas para máquinas virtuais replicadas apenas se a máquina virtual de origem tiver redes aceleradas ativada. Se a máquina virtual de origem não tiver redes aceleradas ativada, pode saber como ativar máquinas de virtuais de Accelerated Networking para Windows [aqui](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Sistemas operativos suportados
As distribuições seguintes são suportadas prontos a utilizar da galeria do Azure:
* **Windows Server 2016 Datacenter**
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>Instâncias VM suportadas
Funcionamento em rede acelerado é suportado em fins mais gerais e tamanhos de instâncias com otimização de computação com vCPUs 2 ou mais.  Esta série suportado é: D/DSv2 e F/Fs

Redes aceleradas é suportada em instâncias que suportam o hyperthreading, nas instâncias VM com vCPUs 4 ou mais. Série suportado é: D/DSv3, E/ESv3, Fsv2 e Ms/Mms

Para obter mais informações sobre as instâncias de VM, consulte [tamanhos de VM do Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="accelerated-networking-with-linux-vms"></a>Redes aceleradas com VMs do Linux

O Azure Site Recovery suporta ativar redes aceleradas para máquinas virtuais replicadas apenas se a máquina virtual de origem tiver redes aceleradas ativada. Se a máquina virtual de origem não tiver redes aceleradas ativada, pode saber como ativar o Accelerated Networking para máquinas virtuais do Linux [aqui](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Sistemas operativos suportados
As distribuições seguintes são suportadas prontos a utilizar da galeria do Azure:
* **Ubuntu 16.04**
* **SLES 12 SP3**
* **RHEL 7.4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian "esticar" com o kernel de backports**
* **Oracle Linux 7.4**

### <a name="supported-vm-instances"></a>Instâncias VM suportadas
Funcionamento em rede acelerado é suportado em fins mais gerais e tamanhos de instâncias com otimização de computação com vCPUs 2 ou mais.  Esta série suportado é: D/DSv2 e F/Fs

Redes aceleradas é suportada em instâncias que suportam o hyperthreading, nas instâncias VM com vCPUs 4 ou mais. Série suportado é: D/DSv3, E/ESv3, Fsv2 e Ms/Mms.

Para obter mais informações sobre as instâncias de VM, consulte [tamanhos de VM do Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>Ativar redes aceleradas para VMs replicadas

Quando [ativar a replicação](azure-to-azure-tutorial-enable-replication.md) para máquinas virtuais do Azure, Site Recovery irá detetar automaticamente se as interfaces de rede de máquina virtual têm redes aceleradas ativada. Se já estiver ativada a redes aceleradas, Site Recovery irá configurar automaticamente o Accelerated Networking nas interfaces de rede da máquina virtual replicada.

O estado das redes aceleradas pode ser verificado no **interfaces de rede** secção a **computação e rede** definições para a máquina virtual replicada.

![Definição de rede acelerada](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

Se tiver ativado o Accelerated Networking na máquina de virtual de origem depois de ativar a replicação, pode ativar a redes aceleradas para interfaces de rede da máquina virtual replicada pelo seguinte processo:
1. Open **computação e rede** definições para a máquina virtual replicada
2. Clique no nome da interface de rede sob o **interfaces de rede** secção
3. Selecione **Enabled** na lista pendente para redes aceleradas sob a **destino** coluna

![Ativar o funcionamento em rede acelerado](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

O processo acima também deve ser seguido para replicados máquinas virtuais existentes, que não tinha anteriormente redes aceleradas ativado automaticamente pelo Site Recovery.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [benefícios de redes aceleradas](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits).
- Saiba mais sobre limitações e restrições de redes aceleradas para [as máquinas virtuais do Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints) e [máquinas virtuais do Linux](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints).
- Saiba mais sobre [planos de recuperação](site-recovery-create-recovery-plans.md) para automatizar a ativação pós-falha do aplicativo.
