---
title: Computação de Alto Desempenho - Azure Virtual Machines Microsoft Docs
description: Saiba mais sobre Computação de Alto Desempenho no Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 723419b97dc024a700d860dd3fe61ff48073a587
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87020003"
---
# <a name="optimization-for-linux"></a>Otimização para Linux

Este artigo mostra algumas técnicas-chave para otimizar a sua imagem de SO. Saiba mais sobre [ativar a InfiniBand](enable-infiniband.md) e otimizar as imagens de SO.

## <a name="update-lis"></a>Atualizar LIS

Se implementar utilizando uma imagem personalizada (por exemplo, um SISTEMA mais antigo, como CentOS/RHEL 7.4 ou 7.5), atualize o LIS no VM.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>Recuperar a memória

Melhore a eficiência recuperando automaticamente a memória para evitar o acesso remoto à memória.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Para que isto persista após a reinicialização do VM:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>Desativar firewall e SELinux

Desative firewall e SELinux.

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## <a name="disable-cpupower"></a>Desativar o poder cpupower

Desativar o poder.

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [ativar a InfiniBand](enable-infiniband.md) e otimizar as imagens de SO.

* Saiba mais sobre [o HPC](/azure/architecture/topics/high-performance-computing/) em Azure.
