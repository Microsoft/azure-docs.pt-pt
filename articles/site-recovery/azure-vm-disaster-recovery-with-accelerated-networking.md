---
title: Permitir a recuperação acelerada de redes para a recuperação de desastres em Azure VM com recuperação do local de Azure
description: Descreve como permitir networking acelerado com recuperação do site Azure para recuperação de desastres de máquina virtual Azure
services: site-recovery
documentationcenter: ''
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: harshacs
ms.openlocfilehash: 1d2d3b3aacc00428c96cde0f8230421a98151ae2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90068018"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>Rede acelerada com recuperação de desastres de máquina virtual Azure

O Networking Acelerado permite uma virtualização de E/S de raiz única (SR-IOV) a um VM, melhorando consideravelmente o seu desempenho em rede. Este percurso de alto desempenho contorna o hospedeiro do datapath, reduzindo a latência, o nervosismo e a utilização do CPU, para utilização com as cargas de trabalho de rede mais exigentes em tipos de VM suportados. A seguinte imagem mostra a comunicação entre dois VMs com e sem rede acelerada:

![Comparação](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

A Recuperação do Site Azure permite-lhe utilizar os benefícios da Rede Acelerada, para máquinas virtuais Azure que são falhadas numa região de Azure diferente. Este artigo descreve como pode ativar a rede acelerada para máquinas virtuais Azure replicadas com a Recuperação do Site Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que compreende:
-   Arquitetura de [replicação](azure-to-azure-architecture.md) de máquina virtual Azure
-   [Criação de replicação](azure-to-azure-tutorial-enable-replication.md) para máquinas virtuais Azure
-   [Falhando mais](azure-to-azure-tutorial-failover-failback.md) Máquinas virtuais Azure

## <a name="accelerated-networking-with-windows-vms"></a>Rede acelerada com VMs do Windows

A Recuperação do Sítio Azure suporta permitir a rede acelerada para máquinas virtuais replicadas apenas se a máquina virtual de origem tiver ativado o Networking Acelerado. Se a sua máquina virtual de origem não tiver rede acelerada ativada, pode aprender a ativar a rede acelerada para máquinas virtuais do Windows [aqui](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Sistemas operativos suportados
As seguintes distribuições são suportadas fora da caixa da Galeria Azure:
* **Windows Server 2016 Datacenter**
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>Instâncias VM apoiadas
O Networking Acelerado é suportado na maioria dos tamanhos de instância otimizados para o cálculo com 2 ou mais vCPUs.  Estas séries suportadas são: D/DSv2 e F/Fs

Em casos que suportam a hiper-leitura, o Networking Acelerado é suportado em instâncias VM com 4 ou mais vCPUs. As séries suportadas são: D/DSv3, E/ESv3, Fsv2 e Ms/Mms

Para obter mais informações sobre as instâncias VM, consulte os [tamanhos do Windows VM](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="accelerated-networking-with-linux-vms"></a>Networking acelerado com VMs Linux

A Recuperação do Sítio Azure suporta permitir a rede acelerada para máquinas virtuais replicadas apenas se a máquina virtual de origem tiver ativado o Networking Acelerado. Se a sua máquina virtual de origem não tiver rede acelerada ativada, pode aprender a ativar a rede acelerada para máquinas virtuais Linux [aqui](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Sistemas operativos suportados
As seguintes distribuições são suportadas fora da caixa da Galeria Azure:
* **Ubuntu 16.04**
* **SLES 12 SP3**
* **RHEL 7.4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian "Stretch" com kernel backports**
* **Oráculo Linux 7.4**

### <a name="supported-vm-instances"></a>Instâncias VM apoiadas
O Networking Acelerado é suportado na maioria dos tamanhos de instância otimizados para o cálculo com 2 ou mais vCPUs.  Estas séries suportadas são: D/DSv2 e F/Fs

Em casos que suportam a hiper-leitura, o Networking Acelerado é suportado em instâncias VM com 4 ou mais vCPUs. As séries suportadas são: D/DSv3, E/ESv3, Fsv2 e Ms/Mms.

Para obter mais informações sobre os casos de VM, consulte os [tamanhos do Linux VM](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>Habilitação de rede acelerada para VMs replicados

Quando [ativa a replicação](azure-to-azure-tutorial-enable-replication.md) de máquinas virtuais Azure, a Recuperação do Site detetará automaticamente se as interfaces de rede de máquinas virtuais têm rede acelerada ativada. Se a Rede Acelerada já estiver ativada, a Recuperação do Site configurará automaticamente a rede acelerada nas interfaces de rede da máquina virtual replicada.

O estado de Rede Acelerada pode ser verificado na secção de **interfaces** de Rede das definições **de Computação e Rede** para a máquina virtual replicada.

![Definição de rede acelerada](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

Se tiver ativado o Networking Acelerado na máquina virtual de origem depois de ativar a replicação, pode ativar a rede acelerada para as interfaces de rede da máquina virtual replicada através do seguinte processo:
1. Configurações de **Open Compute e Network** para a máquina virtual replicada
2. Clique no nome da interface de rede na secção **interfaces da Rede**
3. Selecione **Ativado** a partir do dropdown para rede acelerada sob a coluna **Alvo**

![Ativar networking acelerado](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

O processo acima também deve ser seguido para máquinas virtuais replicadas existentes, que não tinham anteriormente Rede Acelerada ativada automaticamente pela Recuperação do Site.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [os benefícios da Rede Acelerada.](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits)
- Saiba mais sobre limitações e restrições de Rede Acelerada para [máquinas virtuais Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints) e [máquinas virtuais Linux.](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints)
- Saiba mais sobre [planos de recuperação](site-recovery-create-recovery-plans.md) para automatizar o failover da aplicação.