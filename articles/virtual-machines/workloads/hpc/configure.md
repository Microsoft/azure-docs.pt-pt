---
title: Computação de alto desempenho - máquinas virtuais do Azure | Documentos da Microsoft
description: Saiba mais sobre a computação de alto desempenho no Azure.
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
ms.openlocfilehash: 10549abfbdacf1fc1ae6b99f4cab20a290c32a2d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707815"
---
# <a name="optimization-for-linux"></a>Otimização para Linux

Este artigo mostra alguns principais técnicas para otimizar a sua imagem de SO. Saiba mais sobre [permitindo InfiniBand](enable-infiniband.md) e otimizar as imagens de sistema operacional.

## <a name="update-lis"></a>Atualizar o LIS

Se utilizar uma imagem personalizada (por exemplo, um SO mais antigo, como o CentOS/RHEL 7.4 ou 7.5) a implementar, atualize LIS na VM.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>Recuperar memória

Melhore a eficiência ao automaticamente reclamação memória para evitar o acesso remoto à memória.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Para tornar isso sejam mantidas após a VM é reiniciada:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>Desativar a firewall e SELinux

Desative a firewall e SELinux.

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## <a name="disable-cpupower"></a>Desativar cpupower

Desative cpupower.

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [permitindo InfiniBand](enable-infiniband.md) e otimização de imagens do sistema operacional.

* Saiba mais sobre [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) no Azure.
