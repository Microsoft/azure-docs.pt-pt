---
title: Habilitar InifinBand com SR-IOV-máquinas virtuais do Azure | Microsoft Docs
description: Saiba como habilitar o InfiniBand com SR-IOV.
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
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196770"
---
# <a name="enable-infiniband-with-sr-iov"></a>Habilitar InfiniBand com SR-IOV

As séries de VMs NC, ND e H do Azure são todas apoiadas por uma rede InfiniBand dedicada. Todos os tamanhos habilitados para RDMA são capazes de aproveitar essa rede usando o Intel MPI. Algumas séries de VMs têm suporte expandido para todas as implementações de MPI e verbos RDMA por meio de SR-IOV. As VMs compatíveis com RDMA incluem VMs [otimizadas para GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) e [HPC (computação de alto desempenho)](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) .

## <a name="choose-your-installation-path"></a>Escolha o caminho de instalação

Para começar, a opção mais simples é usar uma imagem de plataforma pré-configurada para InfiniBand, quando disponível:

- **VMs de IaaS HPC** – para começar com VMs IaaS para HPC, a solução mais simples é usar a [imagem do sistema operacional VM CentOS-HPC 7,6](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557), que já está configurada com InfiniBand. Como essa imagem já está configurada com InfiniBand, você não precisa configurá-la manualmente. Para versões compatíveis do Windows, consulte [instâncias compatíveis com o Windows RDMA](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances).

- **VMs IaaS de GPU** – nenhuma imagem de plataforma é pré-configurada atualmente para VMs otimizadas para GPU, exceto para [imagens de sistema operacional de VM CentOS-HPC 7,6](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557). Para configurar uma imagem personalizada com o InfiniBand, consulte [instalar manualmente o MELLANOX ofed](#manually-install-mellanox-ofed).

Se você estiver usando uma imagem de VM personalizada ou uma VM [otimizada para GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) , deverá configurá-la com o InfiniBand adicionando a extensão de VM InfiniBandDriverLinux ou InfiniBandDriverWindows à sua implantação. Saiba como usar essas extensões de VM com o [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) e o [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances).

## <a name="manually-install-mellanox-ofed"></a>Instalar o Mellanox OFED manualmente

Para configurar manualmente o InfiniBand com SR-IOV, use as etapas a seguir. O exemplo nessas etapas mostra a sintaxe para RHEL/CentOS, mas as etapas são gerais e podem ser usadas para qualquer sistema operacional compatível, como Ubuntu (16, 4, 18, 4 19, 4) e SLES (12 SP4 e 15). Os drivers de caixa de entrada também funcionam, mas os drivers Mellanox OpenFabrics fornecem mais recursos.

Para obter mais informações sobre as distribuições com suporte para o driver Mellanox, consulte os [drivers Mellanox OpenFabrics](https://www.mellanox.com/page/products_dyn?product_family=26)mais recentes. Para obter mais informações sobre o driver Mellanox OpenFabrics, consulte o [Guia do usuário do Mellanox](https://docs.mellanox.com/category/mlnxofedib).

Consulte o seguinte exemplo de como configurar o InfiniBand no Linux:

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

Para o Windows, baixe e instale os [drivers MELLANOX ofed for Windows](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34).

## <a name="enable-ip-over-infiniband"></a>Habilitar IP sobre InfiniBand

Use os comandos a seguir para habilitar o IP sobre InfiniBand.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) no Azure.
