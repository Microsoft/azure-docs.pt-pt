---
title: Tamanhos disponíveis para Serviços Azure Cloud (suporte alargado)
description: Tamanhos disponíveis para implementações de Azure Cloud Services (suporte alargado)
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: cd8011782d134031393731a29594d44aba41b2ef
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713308"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Tamanhos disponíveis para Serviços Azure Cloud (suporte alargado)

Este artigo descreve os tamanhos de máquinas virtuais disponíveis para os casos de Serviços cloud (suporte alargado).   

| Família de SKU |  ACU/ Núcleo | 
|---|---|
| [A5-7](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#a-series)| 100 |
|[A8-A11](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#a-series---compute-intensive-instances) | 225* |
|[Av2](../virtual-machines/av2-series.md) | 100 | 
|[D](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#d-series) | 160 | 
|[Dv2](../virtual-machines/dv2-dsv2-series.md) | 160 - 190* |
|[Dv3](../virtual-machines/dv3-dsv3-series.md) | 160 - 190* |
|[Ev3](../virtual-machines/ev3-esv3-series.md) | 160 - 190*
|[G](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#g-series) | 180-240* |
|[H](../virtual-machines/h-series.md) | 290 - 300* | 

>[!NOTE]
> As ACUs marcadas com um * utilizam a tecnologia Intel® Turbo para aumentar a frequência de CPU e proporcionar um aumento do desempenho. A quantidade do aumento pode variar consoante o tamanho da VM, a carga de trabalho e outras cargas de trabalho em execução no mesmo anfitrião.

## <a name="configure-sizes-for-cloud-services-extended-support"></a>Configure tamanhos para serviços em nuvem (suporte alargado)

Pode especificar o tamanho da máquina virtual de uma instância de função como parte do modelo de serviço no ficheiro de definição de serviço. O tamanho da função determina o número de núcleos de CPU, capacidade de memória e o tamanho do sistema de ficheiros local.

Por exemplo, definir o tamanho da instância de função web `Standard_D2` para: 

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2"> 
</WorkerRole> 
```

## <a name="change-the-size-of-an-existing-role"></a>Alterar o tamanho de um papel existente

Para alterar o tamanho de uma função existente, altere o tamanho da máquina virtual no ficheiro de definição de serviço (csdef), reembale o seu Serviço cloud e reimplante-o. 

## <a name="get-a-list-of-available-sizes"></a>Obtenha uma lista de tamanhos disponíveis 

Para obter uma lista de tamanhos disponíveis consulte [Resource Skus - List](/rest/api/compute/resourceskus/list) e aplique os seguintes filtros:


`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


## <a name="next-steps"></a>Passos seguintes 
- Reveja os [pré-requisitos](deploy-prerequisite.md) de implantação para serviços em nuvem (suporte alargado).
- Reveja [perguntas frequentes](faq.md) para serviços cloud (suporte alargado).
- Implementar um Serviço de Cloud (suporte alargado) utilizando o [portal Azure](deploy-portal.md), [PowerShell,](deploy-powershell.md) [Modelo](deploy-template.md) ou [Estúdio Visual](deploy-visual-studio.md).