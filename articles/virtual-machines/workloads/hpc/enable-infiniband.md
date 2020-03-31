---
title: Enable InifinBand com SR-IOV - Máquinas Virtuais Azure [ Microsoft Docs
description: Aprenda a ativar a InfiniBand com o SR-IOV.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/17/2019
ms.author: amverma
ms.openlocfilehash: 7f7907482da886d9da17ef1e7844b205f3e4b906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196770"
---
# <a name="enable-infiniband-with-sr-iov"></a>Ativar a InfiniBand com o SR-IOV

As séries Azure NC, ND e H de VMs são apoiadas por uma rede dedicada infiniBand. Todos os tamanhos ativados por RDMA são capazes de alavancar essa rede usando mpi intel. Algumas séries VM expandiram o suporte para todas as implementações de MPI e verbos RDMA através do SR-IOV. VMs capazes de RDMA incluem [GPU otimizado](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) e VMs [de alta performance (HPC).](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc)

## <a name="choose-your-installation-path"></a>Escolha o seu caminho de instalação

Para começar, a opção mais simples é utilizar uma imagem de plataforma pré-configurada para a InfiniBand, sempre que disponível:

- **HPC IaaS VMs** – Para começar com VMs IaaS para HPC, a solução mais simples é utilizar a [imagem CentOS-HPC 7.6 VM OS,](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)que já está configurada com a InfiniBand. Uma vez que esta imagem já está configurada com a InfiniBand, não é preciso configurá-la manualmente. Para versões windows compatíveis, consulte [instâncias compatíveis com o Windows RDMA](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances).

- **VMs GPU IaaS** – Nenhuma imagem da plataforma está atualmente pré-configurada para VMs otimizados por GPU, com exceção da [imagem do OsS OS Do CentOS-HPC 7.6 VM](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557). Para configurar uma imagem personalizada com a InfiniBand, consulte [Manualmente instalar Mellanox OFED](#manually-install-mellanox-ofed).

Se estiver a utilizar uma imagem VM personalizada ou um VM otimizado por [GPU,](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) deve configurá-la com a InfiniBand adicionando à sua implementação a extensão InfiniBandDriverLinux ou InfiniBandDriverWindows VM. Saiba como utilizar estas extensões VM com [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) e [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances).

## <a name="manually-install-mellanox-ofed"></a>Instale manualmente Mellanox OFED

Para configurar manualmente a InfiniBand com o SR-IOV, utilize os seguintes passos. O exemplo nestes passos mostra sintaxe para RHEL/CentOS, mas os passos são gerais e podem ser usados para qualquer sistema operativo compatível como Ubuntu (16.04, 18.04 19.04) e SLES (12 SP4 e 15). Os condutores da caixa de entrada também funcionam, mas os condutores da Mellanox OpenFabrics fornecem mais funcionalidades.

Para obter mais informações sobre as distribuições suportadas para o condutor da Mellanox, consulte os mais recentes [pilotos da Mellanox OpenFabrics.](https://www.mellanox.com/page/products_dyn?product_family=26) Para obter mais informações sobre o controlador Mellanox OpenFabrics, consulte o guia de [utilizador da Mellanox](https://docs.mellanox.com/category/mlnxofedib).

Consulte o seguinte exemplo de como configurar a InfiniBand no Linux:

```bash
# Modify the variable to desired Mellanox OFED version
MOFED_VERSION=#4.7-1.0.0.1
# Modify the variable to desired OS
MOFED_OS=#rhel7.6
pushd /tmp
curl -fSsL https://www.mellanox.com/downloads/ofed/MLNX_OFED-${MOFED_VERSION}/MLNX_OFED_LINUX-${MOFED_VERSION}-${MOFED_OS}-x86_64.tgz | tar -zxpf -
cd MLNX_OFED_LINUX-*
sudo ./mlnxofedinstall
popd
```

Para Windows, descarregue e instale o [Mellanox OFED para controladores Windows](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34).

## <a name="enable-ip-over-infiniband"></a>Ativar IP sobre InfiniBand

Utilize os seguintes comandos para ativar o IP sobre o InfiniBand.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) no Azure.
