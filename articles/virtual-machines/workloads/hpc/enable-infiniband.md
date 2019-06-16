---
title: Ativar InifinBand com SR-IOV - máquinas virtuais do Azure | Documentos da Microsoft
description: Saiba como ativar InfiniBand com SR-IOV.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 81acb804ed2ebb9e88bc7d8281a7fa52359d4455
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66810086"
---
# <a name="enable-infiniband-with-sr-iov"></a>Ativar InfiniBand com SR-IOV


A forma mais simples e recomendada para configurar a sua imagem VM personalizada com InfiniBand (IB) é adicionar a extensão InfiniBandDriverLinux ou InfiniBandDriverWindows VM à sua implementação.
Saiba como utilizar estas extensões VM com [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) e [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)

Configurar manualmente o InfiniBand SR-iov ativado VMs (atualmente série HB e HC), siga os passos abaixo. Estes passos destinam-se apenas ao RHEL/CentOS. Para Ubuntu (16.04 e 18.04) e SLES (12 SP4 e 15), os controladores de caixa de entrada funcionam bem. Para Ubuntu, 


## <a name="manually-install-ofed"></a>Instalar manualmente o OFED

Instale os controladores MLNX_OFED mais recente para ConnectX-5 partir [Mellanox](http://www.mellanox.com/page/products_dyn?product_family=26).

Para RHEL/CentOS (o exemplo abaixo 7,6):
```bash
sudo yum install -y kernel-devel python-devel
sudo yum install -y redhat-rpm-config rpm-build gcc-gfortran gcc-c++
sudo yum install -y gtk2 atk cairo tcl tk createrepo
wget --retry-connrefused --tries=3 --waitretry=5 http://content.mellanox.com/ofed/MLNX_OFED-4.5-1.0.1.0/MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
tar zxvf MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
sudo ./MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64/mlnxofedinstall --add-kernel-support
```

Para Windows, transfira e instale os controladores de WinOF-2 para ConnectX-5 de [Mellanox](http://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)

## <a name="assign-an-ip-address"></a>Atribuir um endereço IP

Atribua um endereço IP para a interface de ib0, através de um:

- Atribua manualmente o endereço IP para a Interface de ib0 (como raiz).

    ```bash
    ifconfig ib0 $(sed '/rdmaIPv4Address=/!d;s/.*rdmaIPv4Address="\([0-9.]*\)".*/\1/' /var/lib/waagent/SharedConfig.xml)/16
    ```

OU

- Utilize WALinuxAgent para atribuir o endereço IP e torná-lo a manter.

    ```bash
    yum install -y epel-release
    yum install -y python-pip
    python -m pip install --upgrade pip setuptools wheel
    wget "https://github.com/Azure/WALinuxAgent/archive/release-2.2.36.zip"
    unzip release-2.2.36.zip
    cd WALinuxAgent*
    python setup.py install --register-service --force
    sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
    sed -i -e 's/# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
    systemctl restart waagent
    ```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) no Azure.
