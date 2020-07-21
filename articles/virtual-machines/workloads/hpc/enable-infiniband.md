---
title: Ativar InifinBand com SR-IOV - Azure Virtual Machines Microsoft Docs
description: Saiba como ativar a InfiniBand com a SR-IOV.
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
ms.openlocfilehash: de61403b62f80bea7872d5ab3561567ae2109590
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500073"
---
# <a name="enable-infiniband-with-sr-iov"></a>Ativar a InfiniBand com a SR-IOV

As séries Azure NC, ND e H de VMs são todas apoiadas por uma rede infiniband dedicada. Todos os tamanhos habilitados por RDMA são capazes de alavancar essa rede utilizando o INTEL MPI. Algumas séries VM expandiram o suporte para todas as implementações de MPI e verbos RDMA através do SR-IOV. Os VMs capazes de RDMA incluem [VMs otimizados](../../sizes-gpu.md) de GPU e VMs [de computação de alto desempenho (HPC).](../../sizes-hpc.md)

## <a name="choose-your-installation-path"></a>Escolha o seu caminho de instalação

Para começar, a opção mais simples é usar uma imagem de plataforma pré-configurada para InfiniBand, sempre que disponível:

- **HPC IaaS VMs** – Para começar com IaaS VMs para HPC, a solução mais simples é utilizar a [imagem CentOS-HPC 7.6 VM OS](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557), que já está configurada com a InfiniBand. Uma vez que esta imagem já está configurada com a InfiniBand, não é necessário configurá-la manualmente. Para ver versões compatíveis com o Windows, consulte [instâncias com capacidade para RDMA do Windows](../../sizes-hpc.md#rdma-capable-instances).

- **GPU IaaS VMs** – Nenhuma imagem de plataforma está atualmente pré-configurada para VMs otimizados gpu, exceto para [a imagem centos-HPC 7.6 VM OS](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557). Para configurar uma imagem personalizada com a InfiniBand, consulte [manualmente instalar Mellanox OFED](#manually-install-mellanox-ofed).

Se estiver a utilizar uma imagem VM personalizada ou um VM [otimizado da GPU,](../../sizes-gpu.md) deverá configugá-la com a InfiniBand adicionando a extensão VM InfiniBandDriverLinux ou InfiniBandDriverWindows VM à sua implementação. Saiba como utilizar estas extensões VM com [Linux](../../sizes-hpc.md#rdma-capable-instances) e [Windows](../../sizes-hpc.md#rdma-capable-instances).

## <a name="manually-install-mellanox-ofed"></a>Instalar manualmente Mellanox OFED

Para configurar manualmente a InfiniBand com a SR-IOV, utilize os seguintes passos. O exemplo nestes passos mostra a sintaxe para RHEL/CentOS, mas os passos são gerais e podem ser utilizados para qualquer sistema operativo compatível, como ubuntu (16.04, 18.04 19.04) e SLES (12 SP4 e 15). Os condutores de caixas de entrada também funcionam, mas os condutores do Mellanox OpenFabrics fornecem mais funcionalidades.

Para obter mais informações sobre as distribuições suportadas para o condutor mellanox, consulte os mais recentes [pilotos da Mellanox OpenFabrics](https://www.mellanox.com/page/products_dyn?product_family=26). Para obter mais informações sobre o controlador Mellanox OpenFabrics, consulte o guia de [utilizador mellanox](https://docs.mellanox.com/category/mlnxofedib).

Veja o seguinte exemplo para como configurar a InfiniBand no Linux:

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

Para windows, descarregue e instale o [Mellanox OFED para controladores Windows](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34).

## <a name="enable-ip-over-infiniband"></a>Ativar IP sobre InfiniBand

Utilize os seguintes comandos para ativar o IP sobre a InfiniBand.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre [o HPC](/azure/architecture/topics/high-performance-computing/) em Azure.
