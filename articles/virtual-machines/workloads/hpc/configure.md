---
title: Configuração e Otimização da InfiniBand ativadas pela série H e máquinas virtuais da série N Azure
description: Saiba como configurar e otimizar a série H ativada pela InfiniBand e VMs da série N para HPC.
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 10/23/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: b1686b08608e4f1c49cd918e86e8149f0fe2a21c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963373"
---
# <a name="configure-and-optimize-vms"></a>Configurar e otimizar VMs

Este artigo partilha técnicas conhecidas para configurar e otimizar as [séries H](../../sizes-hpc.md) e VMs da [série N](../../sizes-gpu.md) ativadas pela InfiniBand para o HPC.

## <a name="vm-images"></a>Imagens de VM
Nos VMs ativados pela InfiniBand, os controladores adequados são obrigados a ativar o RDMA. No Linux, as imagens CentOS-HPC VM no Marketplace vêm pré-configuradas com os controladores apropriados. As imagens Ubuntu VM podem ser configuradas com os controladores certos usando as [instruções aqui](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351). Recomenda-se também criar [imagens VM personalizadas](../../linux/tutorial-custom-images.md) com os controladores e configurações apropriados e reutilizá-las de forma recorrente.

> [!NOTE]
> Nos VMs da [série N](../../sizes-gpu.md) ativados pela GPU, são necessários os condutores adequados da GPU que podem ser adicionados através das [extensões VM](../../extensions/hpccompute-gpu-linux.md) ou [manualmente](../../linux/n-series-driver-setup.md). Algumas imagens VM no Marketplace também vêm pré-instaladas com os controladores da Nvidia GPU.

### <a name="centos-hpc-vm-images"></a>Imagens CentOS-HPC VM

#### <a name="non-sr-iov-enabled-vms"></a>VMs não SR-IOV
Para [vMs com capacidade para RDMA](../../sizes-hpc.md#rdma-capable-instances)não-SR-IOV , a versão 6.5 do CentOS-HPC ou uma versão posterior, até 7.5 no Mercado são adequadas. Como exemplo, para [VMs da série H16, recomenda-se](../../h-series.md)versões 7.1 a 7.5. Estas imagens VM vêm pré-carregadas com os controladores Network Direct para RDMA e Intel MPI versão 5.1.

> [!NOTE]
> Nestas imagens HPC baseadas em CentOS para VMs não ativados por SR-IOV, as atualizações de kernel são desativadas no ficheiro de configuração **yum.** Isto porque os controladores RDMA Do NetworkDirect Linux são distribuídos como um pacote RPM, e as atualizações do condutor podem não funcionar se o kernel for atualizado.

#### <a name="sr-iov-enabled-vms"></a>VMs ativados SR-IOV
  Para [os VMs com capacidade](../../sizes-hpc.md#rdma-capable-instances)SR-IOV, a [versão 7.6 do CentOS-HPC ou uma](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) versão posterior de VM no Mercado são adequadas. Estas imagens VM vêm otimizadas e pré-carregadas com os condutores ofed para RDMA e várias bibliotecas de MPI comumente usadas e pacotes de computação científica e são a maneira mais fácil de começar.

  Exemplo de scripts utilizados na criação da versão CentOS-HPC 7.6 e posteriormente imagens VM de uma imagem base CentOS Marketplace estão no [repo azhpc-images](https://github.com/Azure/azhpc-images/tree/master/centos).
  
  > [!NOTE] 
  > As imagens mais recentes do mercado Azure HPC têm Mellanox OFED 5.1 ou superior, que não suportam ConnectX3-Pro cartões InfiniBand. Os tamanhos VM da série N habilitados pela SR-IOV com FDR InfiniBand (por exemplo, NCv3) poderão utilizar as seguintes versões de imagem CentOS-HPC VM ou mais antigas:
  >- OpenLogic:CentOS-HPC:7.6:7.6.2020062900
  >- OpenLogic:CentOS-HPC:7_6gen2:7.6.2020062901
  >- OpenLogic:CentOS-HPC:7.7:7.7.2020062600
  >- OpenLogic:CentOS-HPC:7_7-gen2:7.7.2020062601
  >- OpenLogic:CentOS-HPC:8_1:8.1.2020062400
  >- OpenLogic:CentOS-HPC:8_1-gen2:8.1.2020062401


### <a name="rhelcentos-vm-images"></a>Imagens RHEL/CentOS VM
As imagens VM não HPC baseadas em RHEL ou CentOS no Mercado podem ser configuradas para utilização nos [VMs com capacidade DE RDMA](../../sizes-hpc.md#rdma-capable-instances)habilitados pela SR-IOV . Saiba mais sobre [a ativação da InfiniBand](enable-infiniband.md) e [a criação de MPI](setup-mpi.md) nos VMs.

  Exemplo de scripts utilizados na criação da versão CentOS-HPC 7.6 e posteriormente imagens VM de uma imagem base CentOS Marketplace estão no [repo azhpc-images](https://github.com/Azure/azhpc-images/tree/master/centos).
  
  > [!NOTE]
  > Mellanox OFED 5.1 ou superior não suporta ConnectX3-Pro cartões InfiniBand em tamanhos VM da série N habilitados com FDR InfiniBand (por exemplo NCv3). Utilize a versão 4.9-0.7.0 ou mais antiga da lts Mellanox OFED na série N com cartões ConnectX3-Pro. Por favor, veja mais detalhes [aqui.](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed)

### <a name="ubuntu-vm-images"></a>Imagens Ubuntu VM
Ubuntu Server 16.04 LTS, 18.04 LTS e 20.04 LTS VM no Mercado são suportados tanto para [VMs com capacidade](../../sizes-hpc.md#rdma-capable-instances)SR-IOV como não-SR-IOV RDMA . Saiba mais sobre [a ativação da InfiniBand](enable-infiniband.md) e [a criação de MPI](setup-mpi.md) nos VMs.

  Exemplo de scripts que podem ser usados na criação das imagens HPC VM baseadas em Ubuntu 18.04 LTS estão no [repo azhpc-images](https://github.com/Azure/azhpc-images/tree/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc).

### <a name="suse-linux-enterprise-server-vm-images"></a>Imagens VM do servidor da empresa SUSE Linux
SLES 12 SP3 para HPC, SLES 12 SP3 para HPC (Premium), SLES 12 SP1 para HPC, SLES 12 SP1 para HPC (Premium), SLES 12 SP4 e SLES 15 VM imagens no Mercado são suportados. Estas imagens VM vêm pré-carregadas com os controladores Network Direct para RDMA e Intel MPI versão 5.1. Saiba mais sobre [a criação de MPI](setup-mpi.md) nos VMs.

## <a name="optimize-vms"></a>Otimizar VMs

Seguem-se algumas definições de otimização opcionais para um melhor desempenho no VM.

### <a name="update-lis"></a>Atualizar LIS

Se necessário para a funcionalidade ou desempenho, [os controladores linux Integration Services (LIS)](../../linux/endorsed-distros.md) podem ser instalados ou atualizados em distros os suportados, especialmente utilizando uma imagem personalizada ou uma versão de SO mais antiga, como CentOS/RHEL 6.x ou versão anterior de 7.x.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>Recuperar a memória

Melhore o desempenho recuperando automaticamente a memória para evitar o acesso remoto à memória.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Para que isto persista após a reinicialização do VM:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>Desativar firewall e SELinux

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

### <a name="disable-cpupower"></a>Desativar o poder cpupower

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

### <a name="configure-walinuxagent"></a>Configure WALinuxAgent

```bash
sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
```
Opcionalmente, o WALinuxAgent pode ser desativado como um passo pré-trabalho e habilitado o pós-trabalho para a máxima disponibilidade de recursos VM para a carga de trabalho do HPC.


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a ativação da InfiniBand](enable-infiniband.md) nas [séries H](../../sizes-hpc.md) e VMs da [série N](../../sizes-gpu.md) ativadas pela InfiniBand.
- Saiba mais sobre a instalação de várias [bibliotecas de MPI suportadas](setup-mpi.md) e a sua configuração ideal nos VMs.
- Reveja a [visão geral](hb-series-overview.md) da série HB e [a visão geral da série HC](hc-series-overview.md) para aprender sobre a configuração ideal das cargas de trabalho para desempenho e escalabilidade.
- Leia sobre os últimos anúncios e alguns exemplos e resultados do HPC no [Azure Compute Tech Community Blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para uma visão arquitetónica de nível mais elevado da execução das cargas de trabalho do HPC, consulte [a High Performance Computing (HPC) em Azure](/azure/architecture/topics/high-performance-computing/).
