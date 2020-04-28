---
title: Ativar a rede acelerada para a recuperação de desastres do Azure VM com a Recuperação do Site Azure
description: Descreve como permitir a recuperação acelerada de networking com o site Azure para a recuperação de desastres de máquinas virtuais azure
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 27691d8fab3e7c8ccd60351dc0be83898ff984ed
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73622437"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>Networking acelerado com recuperação de desastres de máquina virtual Azure

A rede acelerada permite a virtualização de I/O de raiz única (SR-IOV) a um VM, melhorando consideravelmente o seu desempenho em rede. Este caminho de alto desempenho contorna o hospedeiro a partir da trajetória de dados, reduzindo a latência, o nervosismo e a utilização do CPU, para utilização com as cargas de trabalho de rede mais exigentes em tipos de VM suportados. A imagem seguinte mostra a comunicação entre dois VMs com e sem ligação acelerada:

![Comparação](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

A Recuperação do Site Azure permite-lhe utilizar os benefícios da Rede Acelerada, para máquinas virtuais Azure que são falhadas numa região azure diferente. Este artigo descreve como pode ativar a Rede Acelerada para máquinas virtuais Azure replicadas com a Recuperação do Site Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que compreende:
-   Arquitetura de [replicação](azure-to-azure-architecture.md) de máquinas virtuais Azure
-   [Criação de replicação](azure-to-azure-tutorial-enable-replication.md) para máquinas virtuais Azure
-   [Falhando](azure-to-azure-tutorial-failover-failback.md) Máquinas virtuais Azure

## <a name="accelerated-networking-with-windows-vms"></a>Networking acelerado com VMs do Windows

Suportes de recuperação do site Azure que permitem a rede acelerada para máquinas virtuais replicadas apenas se a máquina virtual de origem tiver ativado a rede acelerada. Se a sua máquina virtual de origem não tiver uma Rede Acelerada ativada, pode aprender a ativar a Rede Acelerada para máquinas virtuais windows [aqui](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Sistemas operativos suportados
As seguintes distribuições são suportadas fora da caixa da Galeria Azure:
* **Windows Server 2016 Datacenter**
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>Casos de VM suportados
O Networking Acelerado é suportado na maioria dos tamanhos de instância otimizados para a computação com 2 ou mais vCPUs.  Estas séries apoiadas são: D/DSv2 e F/Fs

Em casos que suportam hiperthreading, o Networking Acelerado é suportado em casos vM com 4 ou mais vCPUs. As séries suportadas são: D/DSv3, E/ESv3, Fsv2 e Ms/Mms

Para obter mais informações sobre os casos de VM, consulte os [tamanhos do Windows VM](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="accelerated-networking-with-linux-vms"></a>Networking acelerado com VMs Linux

Suportes de recuperação do site Azure que permitem a rede acelerada para máquinas virtuais replicadas apenas se a máquina virtual de origem tiver ativado a rede acelerada. Se a sua máquina virtual de origem não tiver rede acelerada ativada, pode aprender a ativar a Rede Acelerada para máquinas virtuais Linux [aqui](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Sistemas operativos suportados
As seguintes distribuições são suportadas fora da caixa da Galeria Azure:
* **Ubuntu 16.04**
* **SLES 12 SP3**
* **RHEL 7.4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian "Esticar" com núcleo de backports**
* **Oracle Linux 7.4**

### <a name="supported-vm-instances"></a>Casos de VM suportados
O Networking Acelerado é suportado na maioria dos tamanhos de instância otimizados para a computação com 2 ou mais vCPUs.  Estas séries apoiadas são: D/DSv2 e F/Fs

Em casos que suportam hiperthreading, o Networking Acelerado é suportado em casos vM com 4 ou mais vCPUs. As séries suportadas são: D/DSv3, E/ESv3, Fsv2 e Ms/Mms.

Para obter mais informações sobre os casos de VM, consulte os [tamanhos de VM Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>Habilitar a rede acelerada para VMs replicados

Quando [ativa a replicação](azure-to-azure-tutorial-enable-replication.md) para máquinas virtuais Azure, a Recuperação do Site detetará automaticamente se as interfaces de rede de máquinas virtuais têm rede acelerada ativada. Se a Rede Acelerada já estiver ativada, a Recuperação do Site configurará automaticamente a Rede Acelerada nas interfaces de rede da máquina virtual replicada.

O estado da Rede Acelerada pode ser verificado na secção de **interfaces** de rede das definições **Compute e Rede** para a máquina virtual replicada.

![Definição acelerada de networking](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

Se tiver ativado a Rede Acelerada na máquina virtual de origem após a replicação, pode ativar a Rede Acelerada para as interfaces de rede da máquina virtual replicada pelo seguinte processo:
1. Configurações de **Computação aberta e rede** para a máquina virtual replicada
2. Clique no nome da interface de rede sob a secção **interfaces de rede**
3. Selecione **Ativado** a partir do dropdown para Networking Acelerado sob a coluna **Target**

![Ativar o Networking Acelerado](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

O processo acima deve também ser seguido para máquinas virtuais replicadas existentes, que anteriormente não tinham rede acelerada ativada automaticamente pela Recuperação do Local.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [os benefícios do Networking Acelerado.](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits)
- Saiba mais sobre limitações e restrições de Networking Acelerado para [máquinas virtuais Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints) e [máquinas virtuais Linux](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints).
- Saiba mais sobre [os planos](site-recovery-create-recovery-plans.md) de recuperação para automatizar falha na aplicação.
