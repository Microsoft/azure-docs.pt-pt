---
title: Ativar InifinBand em VMs HPC - Azure Virtual Machines | Microsoft Docs
description: Saiba como ativar a InfiniBand em VMs Azure HPC.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 11/06/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: cd4d928217ceba80fa5ea0252a6ed20803a812d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666958"
---
# <a name="enable-infiniband"></a>Ativar o Infiniband

As [séries H](../../sizes-hpc.md) e [VMs da série N](../../sizes-gpu.md) com capacidade [RDMA](../../sizes-hpc.md#rdma-capable-instances) comunicam-se sobre a baixa latência e a rede InfiniBand de alta largura de banda. A capacidade de RDMA sobre tal interligação é fundamental para aumentar a escalabilidade e desempenho das cargas de trabalho de HPC e IA distribuídas. Os VMs da série H e vMs da série H ativados infiniband estão ligados numa árvore de gordura que não bloqueia com um design de baixo diâmetro para um desempenho RDMA otimizado e consistente.

Existem várias formas de ativar a InfiniBand nos tamanhos VM capazes.

## <a name="vm-images-with-infiniband-drivers"></a>Imagens VM com condutores InfiniBand
Consulte [as Imagens VM](configure.md#vm-images) para obter uma lista de Imagens VM suportadas no Mercado, que vêm pré-carregadas com controladores InfiniBand (para VMs SR-IOV ou não-SR-IOV) ou podem ser configuradas com os controladores apropriados.
Para os [VMs com capacidade](../../sizes-hpc.md#rdma-capable-instances)SR-IOV, [a versão 7.6 do CentOS-HPC ou uma](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) versão posterior de imagens VM no Mercado são a forma mais fácil de começar.
As imagens Ubuntu VM podem ser configuradas com os controladores certos tanto para os VMs sr-IOV como para não-SR-IOV utilizando as [instruções aqui](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351).

## <a name="infiniband-driver-vm-extensions"></a>Extensões VM do condutor infiniband
No Linux, a [extensão VM InfiniBandDriverLinux](../../extensions/hpc-compute-infiniband-linux.md) pode ser utilizada para instalar os controladores Mellanox OFED e ativar a InfiniBand nos VMs ativados pela SÉRIE H e N ativadas pela SR-IOV.

No Windows, a [extensão VM InfiniBandDriverWindows](../../extensions/hpc-compute-infiniband-windows.md) instala controladores Windows Network Direct (em VMs não-SR-IOV) ou condutores Mellanox OFED (em VMs SR-IOV) para conectividade RDMA. Em certas implementações de instâncias A8 e A9, a extensão HpcVmDrivers é adicionada automaticamente. Note que a extensão VM HpcVmDrivers está a ser depreciada; não será atualizado.

Para adicionar a extensão VM a um VM, pode utilizar cmdlets [Azure PowerShell.](/powershell/azure/) Para obter mais informações, consulte [extensões e funcionalidades da máquina virtual.](../../extensions/overview.md) Também pode trabalhar com extensões para VMs implantados no [modelo clássico de implantação.](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic)

## <a name="manual-installation"></a>Instalação manual
[Os controladores Mellanox OpenFabrics (OFED)](https://www.mellanox.com/products/InfiniBand-VPI-Software) podem ser instalados manualmente nos VMs da série H e da série N [ativados sr-IOV.](../../sizes-hpc.md#rdma-capable-instances) [](../../sizes-hpc.md) [](../../sizes-gpu.md)

### <a name="linux"></a>Linux
Os [controladores OFED para Linux](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed) podem ser instalados com o exemplo abaixo. Embora o exemplo aqui seja para RHEL/CentOS, mas os passos são gerais e podem ser usados para qualquer sistema operativo Linux compatível, como Ubuntu (16.04, 18.04 19.04, 20.04) e SLES (12 SP4 e 15). Mais exemplos para outros distros está no [repo azhpc-images](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mellanoxofed.sh). Os condutores de caixas de entrada também funcionam, mas os condutores da Mellanox OFED fornecem mais funcionalidades.

```bash
MLNX_OFED_DOWNLOAD_URL=http://content.mellanox.com/ofed/MLNX_OFED-5.0-2.1.8.0/MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz
# Optionally verify checksum
wget --retry-connrefused --tries=3 --waitretry=5 $MLNX_OFED_DOWNLOAD_URL
tar zxvf MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz

KERNEL=( $(rpm -q kernel | sed 's/kernel\-//g') )
KERNEL=${KERNEL[-1]}
# Uncomment the lines below if you are running this on a VM
#RELEASE=( $(cat /etc/centos-release | awk '{print $4}') )
#yum -y install http://olcentgbl.trafficmanager.net/centos/${RELEASE}/updates/x86_64/kernel-devel-${KERNEL}.rpm
yum install -y kernel-devel-${KERNEL}
./MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64/mlnxofedinstall --kernel $KERNEL --kernel-sources /usr/src/kernels/${KERNEL} --add-kernel-support --skip-repo
```

### <a name="windows"></a>Windows
Para windows, descarregue e instale o [Mellanox OFED para controladores Windows](https://www.mellanox.com/products/adapter-software/ethernet/windows/winof-2).

## <a name="enable-ip-over-infiniband-ib"></a>Ativar IP sobre InfiniBand (IB)
Se planeia gerir empregos de MPI, normalmente não precisa do IPoIB. A biblioteca MPI utilizará a interface de verbos para a comunicação IB (a menos que utilize explicitamente o canal TCP/IP da biblioteca MPI). Mas se tiver uma aplicação que utilize o TCP/IP para comunicação e pretender passar por IB, pode utilizar o IPoIB através da interface IB. Utilize os seguintes comandos (para RHEL/CentOS) para ativar o IP sobre a InfiniBand.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=n/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a instalação de várias [bibliotecas de MPI suportadas](setup-mpi.md) e a sua configuração ideal nos VMs.
- Reveja a [visão geral](hb-series-overview.md) da série HB e [a visão geral da série HC](hc-series-overview.md) para aprender sobre a configuração ideal das cargas de trabalho para desempenho e escalabilidade.
- Leia sobre os últimos anúncios e alguns exemplos e resultados do HPC no [Azure Compute Tech Community Blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para uma visão arquitetónica de nível mais elevado da execução das cargas de trabalho do HPC, consulte [a High Performance Computing (HPC) em Azure](/azure/architecture/topics/high-performance-computing/).
